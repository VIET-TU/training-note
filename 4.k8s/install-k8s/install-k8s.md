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

# 1. Kubernetes Scheduler yêu cầu bộ nhớ ổn định
# Nếu swap được bật, hệ thống có thể chuyển bộ nhớ từ RAM sang swap, làm sai lệch dữ liệu về dung lượng bộ nhớ thực sự có sẵn.
# Điều này khiến Kubernetes không thể phân bổ tài nguyên chính xác, gây ra lỗi hoặc ảnh hưởng đến hiệu suất.

# 3. Hiệu suất và độ ổn định
# Swap làm giảm hiệu suất vì đọc/ghi từ ổ cứng (hoặc SSD) chậm hơn RAM.
# Các ứng dụng containerized cần độ trễ thấp và tài nguyên bộ nhớ ổn định, nên Kubernetes yêu cầu chạy hoàn toàn trên RAM.

# Kể từ Kubernetes 1.8, kubelet sẽ từ chối chạy nếu swap không bị tắt trừ khi bạn bật tùy chọn --fail-swap-on=false (không khuyến nghị).
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
Là một filesystem module được sử dụng bởi container runtime như containerd hoặc Docker.
Cho phép sử dụng OverlayFS, giúp tối ưu hóa việc lưu trữ và chia sẻ filesystem giữa các container.

# br_netfilter
# Là module hỗ trợ network bridge filtering, cần thiết cho Kubernetes để quản lý iptables và Network Policy.
# Giúp các pod trong Kubernetes có thể giao tiếp với nhau đúng cách.
# Hỗ trợ các tính năng như NAT, packet filtering, giúp quản lý mạng dễ dàng hơn.
```

- Tải module vào kernel

```bash
sudo modprobe overlay
sudo modprobe br_netfilter

# 1️⃣ sudo modprobe overlay

# Tải module OverlayFS vào kernel.
# Hệ thống file này giúp container lưu trữ dữ liệu hiệu quả bằng copy-on-write, giảm dung lượng ổ đĩa và tăng tốc độ.
# Docker, containerd, và Kubernetes đều sử dụng OverlayFS để quản lý layer của image và container.
# 2️⃣ sudo modprobe br_netfilter

# Tải module bridge netfilter vào kernel.
# Hỗ trợ Kubernetes quản lý iptables để kiểm soát mạng giữa các pod.
# Giúp các container trong Kubernetes giao tiếp với nhau đúng cách.
```

- Cấu hình hệ thống mạng

```bash
echo "net.bridge.bridge-nf-call-ip6tables = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
echo "net.bridge.bridge-nf-call-iptables = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf

# 1️⃣ net.bridge.bridge-nf-call-ip6tables = 1
# Bật tính năng kiểm soát gói tin IPv6 trên bridge network (cầu nối mạng).
# Kubernetes dùng iptables để quản lý luồng traffic, nếu không bật có thể gây lỗi mạng.
# ✅ Ý nghĩa: Cho phép Kubernetes quản lý gói tin IPv6 giữa các container.
# 📌 Ví dụ: Nếu một container gửi gói tin IPv6, Linux sẽ cho phép iptables xử lý nó thay vì chặn.

# 2️⃣ net.bridge.bridge-nf-call-iptables = 1
# Bật tính năng kiểm soát gói tin IPv4 trên bridge network.
# Giúp các pod và service trong Kubernetes có thể giao tiếp đúng.
# ✅ Ý nghĩa: Cho phép Kubernetes quản lý gói tin IPv4 giữa các container.
# 📌 Ví dụ: Nếu bạn dùng dịch vụ LoadBalancer hoặc NodePort, các gói tin cần đi qua iptables. Nếu không bật, các pod có thể không giao tiếp được với nhau.

# 3️⃣ net.ipv4.ip_forward = 1
# Cho phép Linux chuyển tiếp gói tin (IP forwarding).
# Giúp các node trong cluster có thể giao tiếp với nhau, đảm bảo network giữa các pod hoạt động tốt.
# 🚀 Giả sử bạn có 2 container trên 2 máy khác nhau
# Nếu không cấu hình hệ thống mạng, hai container này không thể kết nối với nhau.
# 📌 Tại sao?
# Linux mặc định chặn việc chuyển tiếp gói tin giữa các mạng (IP forwarding).
# Kubernetes dùng iptables để điều khiển mạng, nhưng Linux có thể không cho phép iptables xử lý gói tin trên bridge network.
```


```bash
# 📌 Ví dụ thực tế về net.bridge.bridge-nf-call-iptables = 1
# 🏗 Tình huống trước khi bật net.bridge.bridge-nf-call-iptables = 1
# - Bạn có một cluster Kubernetes với 2 Node và triển khai một ứng dụng backend và frontend:
#     + Pod Backend chạy trên Node A (có IP: 192.168.1.10)
#     + Pod Frontend chạy trên Node B (có IP: 192.168.1.20)
# 📌 Mục tiêu:
#     + Frontend gửi request đến Backend thông qua Service.
#     + Backend phải nhận được gói tin và phản hồi lại.
# 📌 Vấn đề xảy ra:
#     + Mặc định, Linux không cho phép chuyển tiếp gói tin trên bridge network.
#     + Kubernetes sử dụng iptables để định tuyến gói tin giữa các pod.
# ==> Nếu net.bridge.bridge-nf-call-iptables = 1 chưa được bật, iptables sẽ bị bỏ qua, khiến request từ Frontend không đến được Backend.
# 👉 Kết quả: Frontend không thể gọi API từ Backend. 😢

# 🚀 Sau khi bật net.bridge.bridge-nf-call-iptables = 1

# 📌 Điều gì thay đổi?
# - Linux cho phép iptables xử lý gói tin IPv4 trên bridge network.
# - Khi Frontend gửi request đến Backend, gói tin được iptables kiểm soát và chuyển đến đúng pod Backend.
# ==> Kết quả: Frontend gọi API Backend thành công! 🎉
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

# containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
# Lệnh này tạo file cấu hình mặc định /etc/containerd/config.toml.
# containerd config default xuất cấu hình mặc định, rồi lưu vào /etc/containerd/config.toml.
# >/dev/null 2>&1 giúp ẩn thông tin hiển thị trên terminal.

# sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
# Dòng này bật tính năng SystemdCgroup.
# sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' tìm và thay thế SystemdCgroup = false thành SystemdCgroup = true.

# 📌 Tại sao cần làm vậy?

# Mặc định, containerd dùng cgroup riêng (cgroupfs).
# Kubernetes yêu cầu dùng cgroup của systemd để quản lý tài nguyên tốt hơn.
# Nếu không bật, có thể gặp lỗi khi khởi động kubelet như: `failed to run Kubelet: failed to create cgroup`

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

- Server k8s-master-1

```bash
sudo kubeadm init
```

```bash
devops@k8s-master-1:/root$ mkdir -p $HOME/.kube
devops@k8s-master-1:/root$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
devops@k8s-master-1:/root$  sudo chown $(id -u):$(id -g) $HOME/.kube/config
devops@k8s-master-1:/root$ kubectl get nodes
NAME           STATUS     ROLES           AGE    VERSION
k8s-master-1   NotReady   control-plane   110s   v1.30.6
devops@k8s-master-1:/root$

## Status NotReady bởi vì ta chưa cài đặt network cho cụm k8s nên là nó chưa thể kéo các tài nguyên về
```

- Reset cụm và khởi tạo lại từ đầu

```bash
# Trên cả 3 server
sudo kubeadm reset -f

sudo rm -rf /var/lib/etcd
sudo rm -rf /etc/kubernetes/manifests/*
```

- Triển khai cụm 3 master đồng thời 3 worker luôn

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

```bash
# check chứng chỉ
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -text
```

# Fix registry.k8s.io/pause:3.9

```bash
ctr -n k8s.io images pull registry.k8s.io/pause:3.9

# Sửa 3.8 -> 3.9
vi /etc/containerd/config.toml
registry.k8s.io/pause:3.8 -> registry.k8s.io/pause:3.9

# Restart containerd
systemctl restart containerd
```

- Thêm CNI mặc định để tránh lỗi NotReady

```bash
mkdir -p /etc/cni/net.d
cat <<EOF | sudo tee /etc/cni/net.d/99-loopback.conf
{
    "cniVersion": "0.3.1",
    "name": "loopback",
    "type": "loopback"
}
EOF
```

```bash
systemctl restart kubelet
```

[link fix][https://chatgpt.com/c/67c51665-e24c-8009-95ef-a5ee57820f23]