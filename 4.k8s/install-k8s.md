# - Thêm hosts

```bash
echo -e "192.168.254.100 k8s-master-1" | sudo tee -a /etc/hosts
```

- Cập nhật và nâng cấp hệ thống

```bash
sudo apt update -y && sudo apt upgrade -y
```

- Tạo user devops và chuyển sang user devops

```bash
adduser devops
usermod -aG sudo devops
su devops
cd /home/devops
```

- Tắt swap

```bash
sudo swapoff -a
sudo sed -i '/swap.img/s/^/#/' /etc/fstab

# Hiệu suất và độ ổn định
# Swap làm giảm hiệu suất vì đọc/ghi từ ổ cứng (hoặc SSD) chậm hơn RAM.
# Các ứng dụng containerized cần độ trễ thấp và tài nguyên bộ nhớ ổn định, nên Kubernetes yêu cầu chạy hoàn toàn trên RAM.

```



- Cấu hình module kernel (thiết lập bước để cài containerd, containerd là  container runtime giống docker, containerd là một runtime mà gg cloud đang sử dụng ) 

```bash
sudo tee /etc/modules-load.d/containerd.conf <<EOF

devops@k8s-master-1:/root$ sudo tee /etc/modules-load.d/containerd.conf <<EOF
> overlay
> br_netfilter
> EOF
overlay
br_netfilter
```

```bash
# Hai module của kernel cần thiết cho container networking và filesystem.

# overlay
# Là một filesystem module được sử dụng bởi container runtime như containerd hoặc Docker.

# br_netfilter
# Là module hỗ trợ network bridge filtering, cần thiết cho Kubernetes để quản lý iptables và Network Policy.
```

- Tải module vào kernel

```bash
sudo modprobe overlay
sudo modprobe br_netfilter

```

- Cấu hình hệ thống mạng

```bash
echo "net.bridge.bridge-nf-call-ip6tables = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
echo "net.bridge.bridge-nf-call-iptables = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf

# Bật tính năng kiểm soát gói tin IPv6 trên bridge network (cầu nối mạng).

# Bật tính năng kiểm soát gói tin IPv4 trên bridge network.

# net.ipv4.ip_forward = 1
# Cho phép Linux chuyển tiếp gói tin (IP forwarding).
# Giúp các node trong cluster có thể giao tiếp với nhau, đảm bảo network giữa các pod hoạt động tốt.
```

- Áp dụng cấu hình sysctl

```bash
sudo sysctl --system
```


- Cài đặt các gói cần thiết và thêm kho Docker

```bash
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

- Cài đặt containerd

```bash
sudo apt update -y
sudo apt install -y containerd.io
```

- Cấu hình containerd

- Lệnh này cấu hình containerd để sử dụng cgroup của systemd, giúp quản lý tài nguyên container hiệu quả hơn khi chạy Kubernetes.

```bash
# 1️⃣ Tạo file cấu hình mặc định cho containerd
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
```

- Khởi động containerd
```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
systemctl status containerd
```

- Thêm kho lưu trữ Kubernetes (để cụm k8s hoạt được phài cài 3 thành phần kubelet, kubeadm, kubectl)
```bash
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

- Cài đặt các gói Kubernetes
```bash
sudo apt update -y
sudo apt install -y kubelet kubeadm kubectl
#  khi ta cài đặt xong thì ta nên hold lại version vì khi chạy lệnh apt update rồi apt upgrade thì hệ thống sẽ cập nhật gói package lên và nếu ta hold lại cái version này có thể  mọt vài thành phần cập nhật lên dẫn đến cụm của ta bị lỗi
sudo apt-mark hold kubelet kubeadm kubectl
```


- Triển khai cụm 2 master đồng thời 2 worker luôn

```bash
sudo kubeadm init --control-plane-endpoint "192.168.254.100:6443" --upload-certs
```

```bash
To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf
```

```bash
You can now join any number of the control-plane node running the following command on each as root:

  kubeadm join 192.168.254.100:6443 --token 5r6t2x.wg94auqs7g0w164d \
        --discovery-token-ca-cert-hash sha256:e9f24ab0e2a6bd8af5919c46b0ea27a00cc6faeaf3cc6a439473ad5dbc721eae \
        --control-plane --certificate-key 9c26544cc6445dbba44e9e5b8b8849f49f26ddc2f00aa7a945223683c0902c98

Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use
"kubeadm init phase upload-certs --upload-certs" to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.254.100:6443 --token 5r6t2x.wg94auqs7g0w164d \
        --discovery-token-ca-cert-hash sha256:e9f24ab0e2a6bd8af5919c46b0ea27a00cc6faeaf3cc6a439473ad5dbc721eae
root@k8s-master-1:~#
```

```bash
```bash
# Giúp node master có thể được schedule như node woker
kubectl taint nodes k8s-master-1 node-role.kubernetes.io/control-plane:NoSchedule-
kubectl taint nodes k8s-master-2 node-role.kubernetes.io/control-plane:NoSchedule-
```

- Fix coredns
```bash
 kubectl edit configmap coredns -n kube-system

 ## sửa forward sang dns gg
 forward . 8.8.8.8 8.8.4.4 {}

 # xóa dòng có chữ loop
 loop

```

Cài cni calico
```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

```bash
root@k8s-master-1:~# kubectl get node
NAME           STATUS   ROLES           AGE   VERSION
k8s-master-1   Ready    control-plane   51m   v1.30.10
k8s-master-2   Ready    control-plane   50m   v1.30.10
```