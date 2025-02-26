# Lab
- Server chạy ansible ansible (ubuntu): 160.191.33.70
- Server ubuntu cài etcd: 160.191.33.108 | 192.168.88.81
- Server centos, cài etcd: 160.191.33.239 | 192.168.89.251


- Sửa giá trị ip etcd storage cho phù hợp

```yml
etcd_nodes:
  - name: "teleport-etcd-node1"
    ip: "192.168.88.81"
  - name: "teleport-etcd-node2"
    ip: "192.168.89.251"
```

- Tạo file inventory.ini

```ini
[etcd_nodes]
ubuntu_teleport_client ansible_host=192.168.88.81 etcd_name=teleport-etcd-node1 ansible_python_interpreter=/usr/bin/python3
centos_teleport_client ansible_host=192.168.89.251 etcd_name=teleport-etcd-node2 ansible_python_interpreter=/usr/local/bin/python3.10
```

- Tạo file playbook: teleport_etcd.yml 

```yml
- name: config etcd cluster
  hosts: etcd_nodes
  become: yes
  roles:
    - teleport-ectd

```

- Chạy ansible

```bash
ansible-playbook -i inventory.ini teleport_etcd.yml 
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