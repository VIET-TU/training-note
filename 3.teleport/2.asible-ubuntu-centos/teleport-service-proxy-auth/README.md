# Lab 
- Server chạy ansible ansible (ubuntu): 160.191.33.70
- Server teleport client (ubuntu, cài teleport): 160.191.33.108 | 192.168.88.81
- Server teleport client (centos, cài teleport): 160.191.33.239 | 192.168.89.251


# Để server ansible có thể ssh qua các host định nghĩa trong server
```bash
mkdir /root/.ssh/id_rsa

# copy and paste privary keykey
# test thử bằng cách ssh root@ip ko cần nhập mật khẩu xem đã ok chưa
```

## Tạo SSL cho domain: teleport.gtsc.vn 

```bash
vi teleport.cnf
```

```conf
[ req ]
default_bits       = 4096
prompt             = no
default_md         = sha256
distinguished_name = req_distinguished_name
req_extensions     = v3_ca

[ req_distinguished_name ]
C  = VN
ST = Hanoi
L  = Hanoi
O  = GTSC
CN = *.gtsc.vn

[ v3_ca ]
basicConstraints = critical, CA:TRUE
keyUsage         = keyCertSign, cRLSign, digitalSignature, keyEncipherment
subjectAltName   = @alt_names

[ alt_names ]
DNS.1 = *.gtsc.vn
IP.1  = 160.191.33.108
IP.2  = 160.191.33.239
```

```bash
openssl genrsa -out teleport.key 4096
openssl req -new -key teleport.key -out teleport.csr -config teleport.cnf
openssl x509 -req -days 365 -in teleport.csr -signkey teleport.key -out teleport.crt -extensions v3_ca -extfile teleport.cnf
openssl x509 -in teleport.crt -text -noout | grep -A1 "Basic Constraints"
sudo cp teleport.crt /etc/pki/ca-trust/source/anchors/
sudo update-ca-trust
```

===> Cat 2 file teleport.crt , teleport.key copy vào file main.yaml folder default

```yml
teleport_proxy_https_keypairs_key_file: |
teleport_proxy_https_keypairs_cert_file: |
```

**Lưu ý:**: nhớ add host domain: `teleport.gtsc.vn`

- Sửa ip của etcd stroage

```yml
teleport_storage_etcd_peers:
  - http://192.168.88.81:2379
  - http://192.168.89.251:2379
```

- Tạo file inventory.ini

```ini
[teleport_proxy_auth]
ubuntu_teleport_client ansible_host=192.168.88.81 ansible_python_interpreter=/usr/bin/python3
centos_teleport_client ansible_host=192.168.89.251 ansible_python_interpreter=/usr/local/bin/python3.10
```

- Tạo file playbook

```bash
vi teleport_service_auth_proxy.yml
```

```yml
- name: Config teleport 
  hosts: teleport_proxy_auth
  become: yes
  roles:
    - teleport-service-auth-proxy
```

- Thực thi

```bash
ansible-playbook -i inventory.ini teleport_service_auth_proxy.yml 
```

- debug

```bash
/usr/local/bin/teleport start --config=/etc/teleport/teleport.yaml --debug
journalctl -u teleport -n 20
```

- Tạo user:

```bash
tctl users add admin --roles=editor,access --logins=root
```


- Reset teleport

```bash
sudo systemctl stop teleport
sudo pkill -f teleport
sudo rm -rf /var/lib/teleport
sudo rm -f /etc/teleport.yaml
systemctl disable teleport
rm -rf /etc/systemd/system/teleport.service
```

**Lưu ý**: nếu python trên server host version cũ thì phải cài bản mới hơn ví dụ 3.10.12

- Centos:

```bash
sudo yum update -y
sudo yum groupinstall -y "Development Tools"

sudo yum install -y gcc gcc-c++ make \
    zlib-devel bzip2 bzip2-devel readline-devel \
    sqlite sqlite-devel openssl-devel tk-devel \
    libffi-devel xz-devel wget

cd /usr/src
sudo wget https://www.python.org/ftp/python/3.10.12/Python-3.10.12.tgz

sudo tar xzf Python-3.10.12.tgz
cd Python-3.10.12
sudo ./configure --enable-optimizations
sudo make -j$(nproc)
sudo make altinstall

python3 --version
```

- Cài thêm HAProxy:

```bash
apt install haproxy

cat /etc/teleport.priv.key /etc/teleport.priv.cert > /etc/teleport.priv.pem

vi /etc/haproxy/haproxy.cfg
```

```haproxy.cfg
frontend https_front
    bind teleport.gtsc.vn:443 ssl crt /etc/teleport.priv.pem
    mode tcp
    timeout client 30s
    default_backend proxy

backend proxy
    balance roundrobin
    mode tcp
    timeout connect 5s
    timeout server 30s
    server proxy-1 192.168.88.81:3080 check ssl verify none
    server proxy-2 192.168.89.251:3080 check ssl verify none
```

```bash
systemctl restart haproxy
systemctl enable haproxy
```