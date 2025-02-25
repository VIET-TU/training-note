
# 1. Tạo PID Namespace và Network Namespace

- Chúng ta sẽ tạo hai môi trường độc lập, mỗi môi trường có:
    + PID namespace riêng → Các tiến trình không nhìn thấy nhau.
    + Network namespace riêng → Mạng bị cô lập.

## Bước 1: Tạo môi trường PID + Network Namespace đầu tiên

```bash
sudo unshare --fork --pid --mount-proc --net bash
# --pid → Tạo PID namespace (tiến trình bị cô lập).
# --net → Tạo Network namespace (mạng bị cô lập).
# --fork → Chạy lệnh trong môi trường mới.
# --mount-proc → Gắn /proc riêng.

# ps aux  # Chỉ thấy tiến trình bên trong namespace, không thấy tiến trình từ host.
# ip addr # Không có interface mạng nào từ host.
root@k8s-master-1:~# sudo unshare --fork --pid --mount-proc --net bash
root@k8s-master-1:~# ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
root@k8s-master-1:~# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.1   7636  4180 pts/0    S    14:56   0:00 bash
root           9  0.0  0.0  10072  1544 pts/0    R+   14:56   0:00 ps aux
```

# Bước 2: Tạo một giao diện mạng và kết nối với host
- Trong shell của namespace:

```bash
ip link add veth1 type veth peer name veth-host1
ip link set veth1 up
ip addr add 192.168.1.2/24 dev veth1
```

- Trở lại host (mở một terminal khác):

```bash
sudo ip link set veth-host1 up
sudo ip addr add 192.168.1.1/24 dev veth-host1
```

===== NHáp

Giải thích chi tiết cách tạo và kết nối network namespace với host bằng veth (Virtual Ethernet Pair)

# 1. Khái niệm về Virtual Ethernet Pair (veth)
- veth (Virtual Ethernet Pair) là một cặp giao diện mạng ảo giúp kết nối hai namespace với nhau.
- Mỗi cặp veth hoạt động như một dây cáp ảo, dữ liệu truyền qua một đầu sẽ xuất hiện ở đầu kia.
- Một đầu của veth sẽ nằm trong network namespace, đầu còn lại nằm trong host hoặc một namespace khác.

# 2. Câu lệnh tạo và kết nối mạng giữa namespace và host
## Bước 1: Tạo cặp veth
- Trong network namespace, chạy:
```bash
ip link add veth1 type veth peer name veth-host1
```
🔹 Giải thích:
+ Lệnh này tạo hai interface mạng ảo:
        + veth1: Sẽ được đưa vào namespace.
        + veth-host1: Ở host (mặc định).
+ Khi một gói tin đi vào veth1, nó sẽ xuất hiện ở veth-host1 và ngược lại.

## Bước 2: Đưa veth1 vào namespace

```bash
ip link set veth1 netns <PID hoặc Tên namespace>
```

- hoặc nếu đang trong namespace:

```bash
ip link set veth1 up
```

- Giải thích:
    + ip link set veth1 netns <namespace> → Chuyển veth1 vào network namespace.
    + ip link set veth1 up → Kích hoạt veth1 trong namespace.

## Bước 3: Cấu hình IP cho veth1 trong namespace
```bash
ip addr add 192.168.1.2/24 dev veth1
```
- 🔹 Giải thích:
    + Gán địa chỉ IP 192.168.1.2/24 cho veth1 trong namespace.
    + Điều này giúp veth1 có thể giao tiếp với các địa chỉ trong subnet 192.168.1.0/24.

## Bước 4: Cấu hình veth-host1 trên host
- Chuyển về terminal host, chạy:
```bash
sudo ip link set veth-host1 up
sudo ip addr add 192.168.1.1/24 dev veth-host1
```
🔹 Giải thích:
    + ip link set veth-host1 up → Kích hoạt veth-host1 trên host.
    + ip addr add 192.168.1.1/24 dev veth-host1 → Gán địa chỉ IP cho veth-host1, cùng subnet với veth1.


==== End Nháp ====


===================

# Để kết nối hai network namespace trong Linux bằng bridge, bạn có thể làm như sau:

1. Tạo hai network namespaces

```bash
ip netns add ns1
ip netns add ns2
```
2. Tạo một bridge

```bash
ip link add br0 type bridge
ip link set br0 up
```

3. Tạo các veth pairs để kết nối namespaces với bridge

```bash
ip link add veth1 type veth peer name veth1-br
ip link add veth2 type veth peer name veth2-br
```

4. Kết nối veth vào namespaces

```bash
ip link set veth1 netns ns1
ip link set veth2 netns ns2
```

5. Kết nối đầu còn lại của veth vào bridge

```bash
ip link set veth1-br master br0
ip link set veth2-br master br0
```

- 6. Kích hoạt các interface

```bash
ip netns exec ns1 ip link set veth1 up
ip netns exec ns2 ip link set veth2 up
ip link set veth1-br up
ip link set veth2-br up
```

7. Gán IP cho namespaces

```bash
ip netns exec ns1 ip addr add 192.168.1.1/24 dev veth1
ip netns exec ns2 ip addr add 192.168.1.2/24 dev veth2
```

8. Kiểm tra kết nối

```bash
ip netns exec ns1 ping -c 3 192.168.1.2
```

============== 






================================== Begin =============================

# Network namespace
- Network namspace là khái niệm cho phép bạn cô lập môi trường mạng network trong một host. Namespace phân chia việc sử dụng các khác niệm liên quan tới network như devices, địa chỉ addresses, ports, định tuyến và các quy tắc tường lửa vào trong một hộp (box) riêng biệt, chủ yếu là ảo hóa mạng trong một máy chạy một kernel duy nhất.

- Mỗi network namespaces có bản định tuyến riêng, các thiết lập iptables riêng cung cấp cơ chế NAT và lọc đối với các máy ảo thuộc namespace đó. Linux network namespaces cũng cung cấp thêm khả năng để chạy các tiến trình riêng biệt trong nội bộ mỗi namespace.

===> khi bạn chỉ tạo một network namespace (ip netns add), nó chỉ cô lập về mạng, không ảnh hưởng đến tiến trình.

# Một số thao tác quản lý làm việc với linux network namespace
- Ban đầu, khi khởi động hệ thống Linux, bạn sẽ có một namespace mặc định đã chạy trên hệ thống và mọi tiến trình mới tạo sẽ thừa kế namespace này, gọi là root namespace. Tất cả các quy trình kế thừa network namespace được init sử dụng (PID 1).

![alt text](image-6.png)

## List namespace
- Cách để làm việc với network namespace là sử dụng câu lệnh ip netns (tìm hiểu thêm tại man ip netns)
- Để liệt kê tất cả các network namespace trên hệ thống sử dụng câu lệnh:
```bash
ip netns
# or
ip netns list
```

==> Nếu chưa thêm bất kì network namespace nào thì đầu ra màn hình sẽ để trống. root namespace sẽ không được liệt kê khi sử dụng câu lệnh ip netns list.

## Add namespaces
- Để thêm một network namespace sử dụng lệnh ip netns add <namespace_name>
- Ví dụ: tạo thêm 2 namespace là ns1 và ns2 như sau:

```bash
 ip netns add ns1
 ip netns add ns2
```

![alt text](image-7.png)

- Sử dụng câu lệnh ip netns hoặc ip netns list để hiển thị các namespace hiện tại:

```bash
root@k8s-master-1:~#  ip netns add ns1
 ip netns add ns2
root@k8s-master-1:~# ip netns
ns2
ns1
root@k8s-master-1:~# ip netns list
ns2
ns1
```

- Mỗi khi thêm vào một namespace, một file mới được tạo trong thư mục /var/run/netns với tên giống như tên namespace. (không bao gồm file của root namespace).

```bash
root@k8s-master-1:~# ls -l /var/run/netns
total 0
-r--r--r-- 1 root root 0 Feb 24 14:44 ns1
-r--r--r-- 1 root root 0 Feb 24 14:44 ns2
```

## Executing commands trong namespaces
- Để xử lý các lệnh trong một namespace (không phải root namespace) sử dụng ip netns exec <namespace> <command>
- Ví dụ: chạy lệnh ip a liệt kê địa chỉ các interface trong namespace ns1.

![alt text](image-8.png)

```bash
root@k8s-master-1:~# ip netns exec ns1 ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
```

- Kết quả đầu ra sẽ khác so với khi chạy câu lệnh ip a ở chế độ mặc định (trong root namespace). Mỗi namespace sẽ có một môi trường mạng cô lập và có các interface và bảng định tuyến riêng.

![alt text](image-9.png)

- Để liệt kê tất các các địa chỉ interface của các namespace sử dụng tùy chọn –a hoặc –all như sau:

```bash
root@k8s-master-1:~# ip -a netns  exec ip a

netns: ns2
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

netns: ns1
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

# or
root@k8s-master-1:~# ip --all netns  exec ip a

netns: ns2
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

netns: ns1
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
```
- Để sử dụng các câu lệnh với namespace ta sử dụng command bash để xử lý các câu lệnh trong riêng namespace đã chọn:

```bash
 ip netns exec <namespace_name> bash
 ip a #se chi hien thi thong tin trong namespace <namespace_name> 
```

## Gán interface vào một network namespace

```bash
# - Sử dụng câu lệnh sau để gán interface vào namespace:
ip link set <interface_name> netns <namespace_name>
# Gán một interface if1 vào namespace ns1 sử dụng lệnh sau:
ip link set <interface_name> netns <namespace_name>
```
- Các thao tác khác tương tự như các câu lệnh bình thường, thêm ip netns exec <namespace_name> <command>


1.2.5. Xóa namespace

## Xóa namespace
```bash
ip netns delete <namespace_name>
```
# 2. Một số bài lab thử nghiệm tính năng của linux network namespace
## 2.1. Kết nối 2 namespace sử dụng Openvswitch

- Xét một ví dụ đơn giản, kết nối 2 namespace sử dụng một virtual switch và gửi bản tin ping từ một namespace tới namespace khác.
- 2 virtual switch thông dụng nhất trong hệ thống ảo hóa trên linux là linux `bridge` và `Openvswitch`. Phần lab này sẽ sử dụng `Openvswitch`.

================================

# Containerd

```bash
sudo apt update && sudo apt install -y containerd
sudo systemctl start containerd
sudo systemctl enable containerd
```

## 2. Tạo network namespace và bridge

```bash
sudo ip netns add ns1
sudo ip netns add ns2
```

```bash
sudo ip link add br0 type bridge
sudo ip link set br0 up

# Tạo veth pair để kết nối namespace với bridge
sudo ip link add veth1 type veth peer name veth1-br
sudo ip link add veth2 type veth peer name veth2-br

# Gán veth vào các namespace
sudo ip link set veth1 netns ns1
sudo ip link set veth2 netns ns2

# Gán veth bridge-side vào bridge
sudo ip link set veth1-br master br0
sudo ip link set veth2-br master br0

# Cấu hình địa chỉ IP
sudo ip netns exec ns1 ip addr add 192.168.1.2/24 dev veth1
sudo ip netns exec ns2 ip addr add 192.168.1.3/24 dev veth2
sudo ip addr add 192.168.1.1/24 dev br0

# Bật interface lên
sudo ip netns exec ns1 ip link set veth1 up
sudo ip netns exec ns2 ip link set veth2 up
sudo ip link set veth1-br up
sudo ip link set veth2-br up
```

## 3. Chạy container với ctr

- Chạy hai container trong hai network namespace.

```bash
# install busybox
sudo ctr image pull docker.io/library/busybox:latest

# - Chạy container trong ns1
sudo ip netns exec ns1 ctr run --rm -t --net-host docker.io/library/busybox:latest container1 sh

# Chạy container trong ns2 
sudo ip netns exec ns2 ctr run --rm -t --net-host docker.io/library/busybox:latest container2 sh

```

## 4. Kiểm tra kết nối

```bash
# Trong terminal của container1 (đang trong ns1), chạy:
ping 192.168.1.3

# Trong terminal của container2 (đang trong ns2), chạy:
ping 192.168.1.2
```

# Sử dụng cni

📌 Bước 1: Tải plugin CNI

```bash
# Containerd không cung cấp sẵn quản lý mạng, ta cần tải CNI plugin:
sudo mkdir -p /opt/cni/bin
sudo curl -L -o /opt/cni/bin/cni-plugins.tgz https://github.com/containernetworking/plugins/releases/download/v1.3.0/cni-plugins-linux-amd64-v1.3.0.tgz
sudo tar -xzvf /opt/cni/bin/cni-plugins.tgz -C /opt/cni/bin
```

📌 Bước 2: Tạo cấu hình mạng bridge

```bash
sudo mkdir -p /etc/cni/net.d
cat <<EOF | sudo tee /etc/cni/net.d/10-bridge.conf
{
  "cniVersion": "0.4.0",
  "name": "mynet",
  "type": "bridge",
  "bridge": "cni0",
  "isGateway": true,
  "ipMasq": true,
  "ipam": {
    "type": "host-local",
    "subnet": "192.168.1.0/24",
    "routes": [
      { "dst": "0.0.0.0/0" }
    ]
  }
}
EOF
```

```bash
sudo ctr image pull docker.io/library/busybox:latest

#  Tạo container 1
sudo ctr run --net-host=false --cni --rm -t docker.io/library/busybox:latest container1 sh

#  Tạo container 2
sudo ctr run --net-host=false --cni --rm -t docker.io/library/busybox:latest container2 sh
```

```bash
# Container 1
root@k8s-master-1:~# sudo ctr run --net-host=false --cni --rm -t docker.io/library/busybox:latest container1 sh
/ # ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0@if21: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue 
    link/ether 26:a9:4a:ce:8a:1e brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.2/24 brd 192.168.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::24a9:4aff:fece:8a1e/64 scope link 
       valid_lft forever preferred_lft forever

# Container 2
root@k8s-master-1:~# sudo ctr run --net-host=false --cni --rm -t docker.io/library/busybox:latest container2 sh
/ # ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0@if22: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue 
    link/ether 2a:ef:96:94:5d:68 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.3/24 brd 192.168.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::28ef:96ff:fe94:5d68/64 scope link 
       valid_lft forever preferred_lft forever
```

5️⃣ Kiểm tra kết nối giữa các container

```bash
# Trong terminal của container1, chạy:
ping 192.168.1.3

# Trong terminal của container2, chạy:
ping 192.168.1.2
```
=============== Nháp =================
📌 CNI là gì?
- CNI (Container Network Interface) là một tiêu chuẩn cho việc quản lý mạng trong các container. Nó giúp container có thể giao tiếp với nhau và với bên ngoài bằng cách cung cấp các plugin mạng như:
    + Bridge: Tạo một mạng cầu nối để các container có thể liên lạc.
    + MACVLAN: Gán địa chỉ MAC trực tiếp từ host.
    + Flannel, Calico: Dùng trong Kubernetes để kết nối cluster.
📌 Khi nào cần --cni?
- Khi bạn muốn container tự động kết nối vào mạng do CNI quản lý.
- Khi bạn đã cấu hình CNI `(/etc/cni/net.d/)` và muốn container dùng nó.

====> Containerd sử dụng CNI bằng cách đọc cấu hình trong thư mục /etc/cni/net.d/. Bạn có thể đặt các tệp JSON trong đó để chỉ định plugin bạn muốn.

```bash
root@k8s-master-1:~# ls /opt/cni/bin/
bandwidth  bridge  cni-plugins.tgz  dhcp  dummy  firewall  host-device  host-local  ipvlan  loopback  macvlan  portmap  ptp  sbr  static  tap  tuning  vlan  vrf

# Các plugin phổ biến:

# bridge: Tạo mạng cầu nối để các container giao tiếp.
# macvlan: Cấp địa chỉ MAC thực từ host.
# ipvlan: Tạo subnet riêng.
```

2️⃣ Chỉ định plugin CNI bằng cách tạo cấu hình mạng
- Bạn có thể chỉ định một plugin cụ thể bằng cách tạo một tệp JSON trong /etc/cni/net.d/. Ví dụ:
============== End Nháp ===============




==================================== End ===========================