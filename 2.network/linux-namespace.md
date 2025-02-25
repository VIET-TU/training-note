# Container
- Container là một công nghệ mà cho phép chúng ta chạy một chương trình trong một môi trường độc lập hoàn toàn với các chương trình còn lại trên cùng một máy tính. Vậy container làm được việc đó bằng cách nào? Và thật ra để làm được việc đó thì container nó được xây dựng từ một vài tính năng mới của Linux kernel, trong đó hai tính năng chính là “namespaces” and “cgroups”. Đây là hai tính năng của Linux giúp ta tách biệt một process hoàn toàn độc lập với các process còn lại.

# Linux Namespaces

- Đây là một tính năng của Linux cho phép ta tạo ra một virtualize system, khá giống với chức năng của các công cụ virtual machine. Đây là tính năng chính giúp process của ta tách biệt hoàn toàn với các process còn lại.
- Linux namespaces sẽ bao gồm các thành phần nhỏ hơn như:
    + PID namespace cho phép ta tạo các process tách biệt.
    + Networking namespace cho phép ta chạy chương trình trên bất kì port nào mà không bị xung độ với các process khác chạy trên server.
    + Mount namespace cho phép ta mount và unmount filesystem mà không ảnh hưởng gì tới host filesystem.

```
PID: giúp ta tạo process với PID tách biệt với các process khác trên server.
MNT: giúp ta có thể mount và unmount file mà không ảnh hưởng gì tới file trên server.
NET: giúp ta tạo một network namepsace độc lập.
UTS: giúp process có hostname và domain name riêng biệt.
USER: giúp ta tạo user namespace tách biệt với server.
```


- Thuật ngữ "virtualize system" có thể được hiểu theo nhiều cách, nhưng trong ngữ cảnh của Linux Namespace, nó có nghĩa là một môi trường hệ thống được cô lập nhưng vẫn chạy trên cùng một kernel.

=== 

- Linux Namespace là một cơ chế cung cấp cô lập tài nguyên giữa các tiến trình trên hệ thống Linux. Namespace cho phép tạo ra một môi trường riêng biệt, giúp các tiến trình chạy trong đó không thấy hoặc ảnh hưởng đến các tài nguyên bên ngoài.

#  Lợi ích của Namespace
- Tạo môi trường cô lập: Mỗi tiến trình có thể có không gian riêng cho PID, mạng, hệ thống tập tin, v.v.
- Chạy container: Docker, Kubernetes dùng namespace để cô lập môi trường cho container.
- Tạo môi trường kiểm thử: Chạy ứng dụng trong môi trường riêng mà không ảnh hưởng đến hệ thống chính.

# Dưới đây là chi tiết về cách namespace giúp cô lập từng tài nguyên:

## Cô lập PID (PID Namespace)
- Mỗi PID namespace có tập tiến trình riêng của nó. Một tiến trình trong namespace có thể thấy và quản lý chỉ các tiến trình bên trong namespace của nó, nhưng không thể thấy hoặc ảnh hưởng đến các tiến trình bên ngoài.

➡ Ứng dụng: PID namespace giúp chạy container mà không bị lẫn với các tiến trình khác.

- - Để tạo linux namespace khá đơn giản, ta dùng một package tên là `unshare` để tạo một namespace riêng với process tách biệt với các process còn lại. Ví dụ ta chạy câu lệnh sau để tạo namespace và thực thi câu lệnh bash trên nó.

```bash
sudo unshare --fork --pid --mount-proc bash

# unshare là một lệnh trong Linux dùng để tạo namespace mới, giúp tiến trình chạy trong một không gian cô lập.
# --fork yêu cầu unshare tạo một tiến trình con (fork) và chạy trong namespace mới, giúp tránh trường hợp tiến trình cha vẫn nằm trong namespace cũ.
# Tạo một PID namespace mới, trong đó các tiến trình có thể có PID riêng không bị lẫn với các tiến trình bên ngoài.
# Tiến trình đầu tiên trong namespace mới luôn có PID = 1, tương tự như tiến trình init trong một hệ điều hành Linux.
# Mount lại /proc trong namespace mới, để danh sách PID trong /proc chỉ hiển thị các tiến trình thuộc namespace này.
# Nếu không dùng --mount-proc, tiến trình trong namespace mới vẫn thấy danh sách tiến trình của hệ thống chính.
```

```bash
# Nó sẽ tạo ra một virtualize system và gán bash shell vào nó.
sudo unshare --fork --pid --mount-proc bash

root@viettu:~# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0  23104  4852 pts/0    S    21:54   0:00 bash
root        12  0.0  0.0  37800  3228 pts/0    R+   21:57   0:00 ps aux
```

- Ta sẽ thấy namespace được tạo ra là một môi trường hoàn toàn tách biệt với bên ngoài, nó chỉ có duy nhất hai process đang chạy là bash với câu lệnh ps aux ta vừa gõ.

```bash
# Bạn bật một terminal khác ở trên server và gõ câu lệnh ps aux.
hmquan@viettu:~$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
...
root        43  0.0  0.0   7916   828 pts/0    S    21:54   0:00 unshare --fork --pid --mount-proc bash
...

# - Ta sẽ thấy một process của unshare đang chạy, ta có thể so sánh nó với các container được liệt kê ra khi ta chạy câu lệnh docker ps.
# Để thoát khỏi namespace thì bạn gõ exit.
# - Lúc này khi bạn chạy lại câu lệnh ps aux ở trên server ta sẽ thấy process của unshare hồi nãy đã mất đi.
```

```bash
sudo unshare --fork --pid --mount --uts --mount-proc bash

# Giải thích các tùy chọn:
# --fork: Fork ra một tiến trình mới để chạy lệnh.
# --pid: Tạo PID namespace mới, làm cho tiến trình con có PID bắt đầu từ 1.
# --mount: Tạo mount namespace mới, giúp cô lập hệ thống file.
# --uts: Tạo UTS namespace mới, cho phép thay đổi hostname độc lập.
# --mount-proc: Tự động mount /proc trong namespace mới (điều này giúp lệnh ps hiển thị đúng thông tin các tiến trình trong container).
# bash: Lệnh chạy shell Bash bên trong container.
```



## Cgroups
- Ta đã có thể tạo một process riêng biệt với namespace, nhưng nếu ta tạo nhiều namespace thì làm sao ta giới hạn được resource của từng namespace để nó không chiếm mất resource của namespace khác?
- May thay là Linux cũng đã đoán được điều đó và tạo ra Cgroups, đây là tính năng để giới hạn resource của một process. Cgroups sẽ định ra giới hạn của CPU và Memory mà một process có thể dùng. Để tạo cgroup thì ta sẽ dùng cgcreate. Ta cần cài cgroup-tools trước khi sử dụng.

```bash
sudo apt-get install cgroup-tools
# Sau đó, để tạo cgroup ta chạy câu lệnh sau.
sudo cgcreate -g memory:my-process
# Nó sẽ tạo ra cho ta một folder ở dường dẫn /sys/fs/cgroup/memory, các bạn liệt kê nó ra.
$ ls /sys/fs/cgroup/memory/my-process
memory.kmem.usage_in_bytes          notify_on_release
memory.limit_in_bytes               tasks
memory.max_usage_in_bytes

```

- Ta sẽ thấy khá nhiều file, đây là những file định nghĩa limit của process, file mà ta quan tâm bây giờ là memory.kmem.limit_in_bytes, nó sẽ định nghĩa memory limit của một process, giá trị sử dụng theo bytes nhé. Ví dụ ta giới hạn memory là 50Mi.

```bash
sudo echo 50000000 >  /sys/fs/cgroup/memory/my-process/memory.limit_in_bytes
```

- Ok, sau đó để sử dụng cgroup ta chạy câu lệnh sau.

```bash
hmquan@server:~$ sudo cgexec -g memory:my-process bash
root@cgroup:~#
```

===> Lúc này process được tạo bởi cgroup sẽ có memory limit là 50Mi.

## Cgroups with namespace
- Và ta có thể sử dụng cgroups kết hợp với namespace để tạo một process độc lập và có giới hạn resource nó có thể sử dụng. Ví dụ ta chạy câu sau.

```bash
hmquan@server:~$ sudo cgexec -g cpu,memory:my-process unshare -uinpUrf --mount-proc sh -c "/bin/hostname my-process && chroot mktemp -d /bin/sh"
```

===> Vậy thật ra container là một sự kết hợp của hai tính năng cgroups và namespace, tuy thực tế thì có thể nó còn một số thứ khác nữa, nhưng cơ bản cgroups và namespace là hai cái chính.


## Container and Container runtime?

- Container được tạo ra để giúp ta chạy một chương trình trong một môi trường hoàn toàn độc lập với các chương trình khác trên cùng một máy tính. Nhưng ta sẽ gặp một vài vấn đề sau nếu ta chỉ dùng linux namespace và cgroup để chạy container.

-Vấn đề đầu tiên là để tạo được container thì ta cần chạy khá nhiều câu lệnh, nào là câu lệnh tạo linux namespace, câu lệnh tạo cgroup process, câu lệnh cấu hình limit cho cgroup process, sau đó nếu ta muốn xóa container thì ta phải chạy các câu lệnh để clear namespace và cgroup.

- Và vấn đề thứ hai là khi ta chạy cả chục container bằng câu lệnh linux namespace và cgroup thì làm sao ta quản lý những container đó, ta làm sao biết được thằng container đó nó đang chạy gì và nó được dùng cho process nào?

- Vấn đề thứ ba là có các container có sẵn những thứ ta cần và nó nằm trên container registry, làm sao ta có thể tải nó xuống và chạy thay vì ta phải tạo container từ đầu?

- Với các vấn đề ở trên thì thay vì ta phải chạy nhiều câu lệnh như vậy, thì tại sao ta không xây dựng ra một công cụ nào đó để ta giảm tải việc này, ta chỉ cần chạy một câu lệnh để tạo container và xóa container. Và công cụ đó cũng có có thể giúp ta quản lý được nhiều container đang chạy và ta biết được container đó đang được dùng cho process nào. Và ta cũng có thể dùng công cụ đó để tải các container có sẵn ở trên internet. Đó chính là là lý do tại sao thằng container runtime được sinh ra.

- Container runtime là một công cụ đóng vai trò quản lý tất cả quá trình running của một container, bao gồm tạo và xóa container, đóng gói và chia sẻ container. Container runtime được chia ra làm hai loại:
    + Low-level container runtime: với nhiệm vụ chính là tạo và xóa container.
    + High level container runtime: quản lý container, tải container image sau đó giải nén container image đó ra và truyền vào trong low level container runtime để nó tạo và chạy container.


### Low-level container runtime
- Như ta đã nói ở trên thì nhiệm vụ chính của low-level container runtime là tạo và xóa container, những công việc mà low-level container runtime sẽ làm là:
    + Tạo cgroup.
    + Chạy CLI trong cgroup.
    + Chạy câu lệnh Unshare để tạo namespaces riêng.
    + Cấu hình root filesystem.
    + Clean up cgroup sau khi câu lệnh hoàn tất.

- Thực tế thì low level container runtime sẽ còn làm rất nhiều thứ nữa, nhưng ở trên là những công việc chính. Mô phỏng quá trình container runtime tạo container.

```bash
ROOTFS=$(mktemp -d) && UUID=9999

# Tạo thư mục tạm thời cho root filesystem:
# mktemp -d sẽ tạo ra một thư mục mới với một tên ngẫu nhiên và trả về đường dẫn của thư mục này. Phần ROOTFS=$(mktemp -d) gán giá trị đường dẫn này cho biến ROOTFS. Thư mục tạm này thường được dùng làm root filesystem (hệ thống tập tin gốc) cho container trong quá trình thực thi.

# Đặt giá trị cho biến UUID:
# Phần UUID=9999 gán giá trị 9999 cho biến UUID. Biến này có thể được sử dụng để định danh hoặc làm định danh duy nhất cho container trong các thao tác sau này.

# Tóm lại, lệnh này giúp cấu hình môi trường cho container bằng cách tạo ra thư mục làm root filesystem tạm thời và định nghĩa một giá trị định danh (UUID) cho container.
```


```bash
# - Tạo cgroup.
sudo cgcreate -g cpu,memory:$UUID
# Cấu hình limit memory cho cgroup.
sudo cgset -r memory.limit_in_bytes=100000000 $UUID
# Cấu hình limit CPU cho cgroup.
sudo cgset -r cpu.shares=512 $UUID && sudo cgset -r cpu.cfs_period_us=1000000 $UUID && sudo cgset -r cpu.cfs_quota_us=2000000 $UUID
# Tạo container.
sudo cgexec -g cpu,memory:$UUID unshare -uinpUrf --mount-proc sh -c "/bin/hostname $UUID && chroot $ROOTFS /bin/sh"
# Xóa cgroup.
sudo cgdelete -r -g cpu,memory:$UUID
```



=====

# Cô lập mạng (Network Namespace - NET Namespace)
- Mỗi network namespace có thể có cấu hình mạng riêng biệt, bao gồm:
    + Giao diện mạng (interface).
    + Địa chỉ IP riêng.
    + Bảng định tuyến riêng.
    + Không nhìn thấy các interface mạng khác trừ khi được chia sẻ.

# 3. Cô lập hệ thống tập tin (Mount Namespace - MNT Namespace)
- Mỗi tiến trình trong mount namespace riêng có thể có một hệ thống tập tin riêng, tức là nó có thể:
    + Mount ổ đĩa riêng, không ảnh hưởng đến hệ thống chính.
    + Không thấy các tập tin được mount ở namespace khác.

============================================

# LAB

teleport
10.80.5.90
10.80.5.109
10.80.5.52
etcd - Đã cài xong
10.80.5.143
10.80.5.125
10.80.5.113

vi /etc/default/etcd


sudo systemctl daemon-reload
sudo systemctl enable etcd
sudo systemctl restart etcd

etcdctl --endpoints=http://172.16.3.113:2379 cluster-health
etcdctl --endpoints=http://10.80.5.143:2379 endpoint health


root@teleport-etcd-1:~# ls /var/lib/teleport/
etcd-ca.crt  etcd-ca.key  etcd-cert.pem  etcd.csr  etcd-key.pem


http://172.16.3.113:2380,etcd-node2=http://172.16.3.74:2380,etcd-node3=http://172.16.3.76

etcdctl --endpoints=http://10.80.5.143:2379,http://10.80.5.125:2379,http://10.80.5.113:2379 member list

teleport
160.191.33.95
160.191.33.20
160.191.33.250
etcd - Đã cài xong
160.191.33.32
160.191.33.6
160.191.33.72

/usr/local/bin/teleport start --config=/etc/teleport/teleport.yaml --debug


sudo journalctl -u teleport -f


systemctl daemon-reload
systemctl start teleport
systemctl status teleport






```bash
ETCD_NAME=node1
ETCD_DATA_DIR="/var/lib/etcd"
ETCD_INITIAL_CLUSTER="node1=https://160.191.33.32:2380,node2=https://160.191.33.6:2380,node3=https://160.191.33.72:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_LISTEN_PEER_URLS="https://160.191.33.32:2380"
ETCD_LISTEN_CLIENT_URLS="https://160.191.33.32:2379,http://127.0.0.1:2379"
ETCD_ADVERTISE_CLIENT_URLS="https://160.191.33.32:2379"
ETCD_PEER_CERT_FILE="/etc/etcd/peer.crt"
ETCD_PEER_KEY_FILE="/etc/etcd/peer.key"
ETCD_PEER_TRUSTED_CA_FILE="/etc/etcd/ca.crt"
ETCD_CERT_FILE="/etc/etcd/server.crt"
ETCD_KEY_FILE="/etc/etcd/server.key"
ETCD_TRUSTED_CA_FILE="/etc/etcd/ca.crt"

```

/usr/local/bin/teleport start --config=/etc/teleport/teleport.yaml --debug


 vi /etc/haproxy/haproxy.cfg

 sysctl net.ipv4.ip_forward

 vi /etc/sysctl.conf

sysctl -p


journalctl -u keepalived --no-pager | tail -20

vi /etc/keepalived/keepalived.conf



10.80.5.90 teleport-1
10.80.5.109 teleport-2                                                                                                                                      
10.80.5.52 teleport-3 

teleport start --insecure

tctl users add admin --roles=editor,access --logins=root

--cert /


==============================================




