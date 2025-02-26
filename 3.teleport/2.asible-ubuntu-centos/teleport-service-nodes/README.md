# Lab
- Server chạy ansible ansible (ubuntu): 160.191.33.70
- Server telepoprt agent (ubuntu): 160.191.33.70
- Server teleport agent (centos): 160.191.33.18

#  Từ server teleport tạo token

```bash
 tctl tokens add --type=node --ttl=0

# The invite token: d8957ec663bda1000e23635430ab185f
# This token will expire in 43200 minutes.

# Run this on the new node to join the cluster:

# > teleport start \
#    --roles=node \
#    --token=d8957ec663bda1000e23635430ab185f \
#    --ca-pin=sha256:6e911ba2f4bdcd589b069afb32b1d5c4f990cd28850ddacb38e71e0acf7c26e3 \
#    --auth-server=10.80.5.109:3025

# Please note:

#   - This invitation token will expire in 43200 minutes
#   - 10.80.5.109:3025 must be reachable from the new node
```

- Sửa value teleport_token_name và teleport_ca_pin

```yml
teleport_token_name: c8759922e333c080225a33f932733d0e
teleport_ca_pin: sha256:4c9e0e075d46f71f9d0c8c4a04c91b1eb14b98df80bda5a579134d5c522228f4
```

- **Lưu ý:** nhớ add host domain: teleport.gtsc.vn (một trong 2 server teleport)

- Sửa value teleport_proxy_https_keypairs_cert_file bằng .crt của teleport server

```bash
teleport_proxy_https_keypairs_cert_file: |
```

- Tạo file inventory.ini

```ini
[teleport_node]
ubuntu_teleport_agent ansible_host=160.191.33.70 ansible_python_interpreter=/usr/bin/python3
centos_teleport_agent ansible_host=160.191.33.18 ansible_python_interpreter=/usr/local/bin/python3.10
```

- Tạo file playbook: teleport_node.yml 

```yml
- name: Cấu hình teleport node
  hosts: teleport_node
  become: yes
  roles:
    - teleport-service-nodes
```

- Chạy ansible

```bash
ansible-playbook -i inventory.ini teleport_node.yml 
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