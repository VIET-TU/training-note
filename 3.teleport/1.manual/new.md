# Server Lab
```plaintext
etcd
10.80.5.143
10.80.5.125
10.80.5.113
teleport: auth,proxy,node
10.80.5.90
10.80.5.109
10.80.5.52
# teleport agent (node)
160.191.33.155 
```

# Cài đặt etcd

```bash
apt update
apt install etcd

vi /etc/default/etcd
```

```conf
ETCD_NAME="etcd-node1"
ETCD_DATA_DIR="/var/lib/etcd"
ETCD_LISTEN_PEER_URLS="http://10.80.5.143:2380"
ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0:2379"
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.80.5.143:2380"
ETCD_ADVERTISE_CLIENT_URLS="http://10.80.5.143:2379"
ETCD_INITIAL_CLUSTER="etcd-node1=http://10.80.5.143:2380,etcd-node2=http://10.80.5.125:2380,etcd-node3=http://10.80.5.113:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
```

- Tương tự trên 2 server còn lại

```bash
sudo systemctl daemon-reload
sudo systemctl enable etcd
sudo systemctl restart etcd

# health check
etcdctl --endpoints=http://172.16.3.113:2379 cluster-health
etcdctl --endpoints=http://10.80.5.143:2379 endpoint health
etcdctl --endpoints=http://10.80.5.143:2379,http://10.80.5.125:2379,http://10.80.5.113:2379 member list
```



# self signed certificate

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
IP.1  = 10.80.5.90
IP.2  = 10.80.5.109
IP.2  = 10.80.5.52
```


```bash
openssl genrsa -out teleport.key 4096
openssl req -new -key teleport.key -out teleport.csr -config teleport.cnf
openssl x509 -req -days 365 -in teleport.csr -signkey teleport.key -out teleport.crt -extensions v3_ca -extfile teleport.cnf
openssl x509 -in teleport.crt -text -noout | grep -A1 "Basic Constraints"
mv teleport.crt teleport.key /etc/teleport
```

```bash
scp teleport.key teleport.crt  root@10.80.5.90:/etc/teleport/
scp teleport.key teleport.crt  root@10.80.5.109:/etc/teleport/
scp teleport.key teleport.crt  root@10.80.5.52:/etc/teleport/

```bash
# Update cert
sudo cp teleport.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates
cat /etc/teleport/teleport.crt /etc/teleport/teleport.key > /etc/teleport/teleport.pem

# Check info crt
 openssl x509 -noout -text -in '/etc/teleport/teleport.crt'
```

- Copy cert và key sang khác server còn lại

eport.key teleport.crt  root@10.80.5.52:/etc/teleport/
```

## Add host

```bash
Vi /etc/hosts

# teleport 1
10.80.5.90 teleport.gtsc.vn
# teleport 2
10.80.5.109 teleport.gtsc.vn
# teleport 3
10.80.5.52 teleport.gtsc.vn
```

# Install teleport

```bash
# Install file
wget https://cdn.teleport.dev/teleport-v17.1.6-linux-amd64-bin.tar.gz
tar -xvzf teleport-v17.1.6-linux-amd64-bin.tar.gz
# Giải nén file
tar -xvzf teleport-v17.1.6-linux-amd64-bin.tar.gz

mv teleport/tctl /usr/local/bin/
mv teleport/tsh /usr/local/bin/
mv teleport/teleport /usr/local/bin/
teleport version && tctl version && tsh version
```

## Config teleport


```bash
vi /etc/teleport/teleport.yaml
# or
vi /etc/teleport.yaml
```

```yaml
teleport:
  data_dir: /var/lib/teleport
  log:
    output: stderr
    severity: INFO
    format:
      output: text
  storage:
     type: etcd
     peers: ["http://10.80.5.143:2379","http://10.80.5.125:2379","http://10.80.5.113:2379"]
     prefix: /teleport/
     insecure: true

auth_service:
  enabled: true
  cluster_name: teleport.gtsc.vn
  listen_addr: 0.0.0.0:3025
  proxy_listener_mode: multiplex

proxy_service:
  enabled: true
  web_listen_addr: 0.0.0.0:3080
  public_addr: teleport.gtsc.vn:443
  https_keypairs:
    - cert_file: /etc/teleport/teleport.crt
      key_file: /etc/teleport/teleport.key
```

- Tạo file service

```bash
vi /etc/systemd/system/teleport.service
```

- Nội dung cấu hình

```service
[Unit]
Description=Teleport SSH Service
After=network.target

[Service]
Type=simple
Restart=on-failure
EnvironmentFile=-/etc/teleport/teleport.yaml
ExecStart=/usr/local/bin/teleport start --pid-file=/run/teleport.pid --roles=node,proxy,auth
Environment="SSL_CERT_FILE=teleport.crt"
Environment="SSL_CERT_DIR=/etc/teleport/"
ExecReload=/bin/kill -HUP $MAINPID
PIDFile=/run/teleport.pid
LimitNOFILE=8192

[Install]
WantedBy=multi-user.target
```

## Khởi động teleport

```bash
systemctl daemon-reload
systemctl start teleport
systemctl enable teleport
systemctl status teleport
```

===> Tương tự trên 2 server còn lại

# Cấu hình HAProxy

```bash
apt install haproxy
```

```bash
vi /etc/haproxy/haproxy.cfg
```

```cfg
frontend https_front
    bind 0.0.0.0:443 ssl crt /etc/teleport/teleport.pem
    mode tcp
    timeout client 30s
    default_backend proxy

backend proxy
    balance roundrobin
    mode tcp
    timeout connect 5s
    timeout server 30s
    server proxy-1 10.80.5.90:3080 check ssl verify none
    server proxy-2 10.80.5.109:3080 check ssl verify none
    server proxy-3 10.80.5.52:3080 check ssl verify none
```

## Khởi động

```bash
systemctl restart haproxy
systemctl enable haproxy
# Debug
journalctl -u haproxy --no-pager --since "1 hour ago"
journalctl -u haproxy.service -n 100 --no-pager
# check syntax
haproxy -c -f /etc/haproxy/haproxy.cfg
```

- Khởi động lại teleport và tạo tài khoản

```bash
systemctl restart teleport
tctl users add admin --roles=editor,access --logins=root
```

# Cài teleport agent

## Tạo token vĩnh viễn

```bash
root@teleport-1:~# tctl tokens add --type=node --ttl=720h
The invite token: d8957ec663bda1000e23635430ab185f
This token will expire in 43200 minutes.

Run this on the new node to join the cluster:

> teleport start \
   --roles=node \
   --token=d8957ec663bda1000e23635430ab185f \
   --ca-pin=sha256:6e911ba2f4bdcd589b069afb32b1d5c4f990cd28850ddacb38e71e0acf7c26e3 \
   --auth-server=10.80.5.109:3025

Please note:

  - This invitation token will expire in 43200 minutes
  - 10.80.5.109:3025 must be reachable from the new node
```


```bash
sudo bash -c "$(curl -fsSL https://teleport.gtsc.vn/scripts/d8957ec663bda1000e23635430ab185f/install-node.sh)"
```

**Lưu ý:** Chú ý update cert trên server agent, cách làm đã nói ở trên