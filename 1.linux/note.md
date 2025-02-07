# Showing to which shell /bin/sh points on an Ubuntu distribution

```bash
$ readlink /bin/sh
dash
```

# Khái niệm biến môi trường, cách sử dụng ( cách gán, xem giá trị của biến), cách xem toàn bộ biến môi trường đang có

- Trong Linux (Shell), có 2 loại variable:
    + Biến hệ thống: Được tạo và duy trì bởi chính Linux. Thường được viết theo định dạng UPPERCASE.
    Biến do người dùng định nghĩa (UDV): Được tạo và duy trì bởi người dùng. Thường được viết theo định dạng lowercase.

```bash
#variable_name=variable_value
export VAR_NAME="Hello, world!"
echo $VAR_NAME

# Biến chỉ đọc: giá trị của nó không thể thay đổi - Cú pháp readonly variable_name
readonly variable_name

# delete variable
unset VAR_NAME
# view all varialbe
env
printenv
```

# Cách sử dụng lệnh man để tra cứu, tìm hiểu lệnh và các option

```bash
# man [option(s)] keyword(s)  
man ls
```

**Lưu ý**:
- Nhấn q để thoát.
- Nhấn Space để xuống trang tiếp theo.
- Nhấn b để quay lại trang trước.
- Nhấn / rồi nhập từ khóa để tìm kiếm trong trang (VD: /option).
- Nhấn n để chuyển đến kết quả tiếp theo trong tìm kiếm.


# Nắm các khái niệm về 3 loại streams trong Linux: Standard input,Standard output, Standard error

- stdin: Nhận dữ liệu đầu vào (bàn phím hoặc tệp).
- stdout: Xuất dữ liệu đầu ra (màn hình hoặc tệp).
- stderr: Xuất thông báo lỗi.

```bash
# stdin
$ cat

# stdout
echo "Hello, World!"

#stderr
ls /khong_ton_tai
```

# Redirect Input và Output, cách Piping Data giữa các Programs

```bash
# Redirect output
echo "Hello, World!" > text.txt

# Redirecting Input (Chuyển hướng Input từ tệp)
sort < input.txt

```
## Piping Data giữa các Program
- Pipes (|) cho phép chuyển kết quả từ một lệnh này vào lệnh khác mà không cần phải lưu vào tệp. Điều này giúp các lệnh có thể làm việc liên tiếp, tạo thành một "chuỗi lệnh".

```bash
# command_1 | command_2 | command_3 | .... | command_N 
ls | grep "file"
```

# Hiểu và thành thạo các lệnh quan trọng ‘cat’, ‘join’, ‘paste’ cùng các option quan trọng đi kèm

- cat: 
    + Hiển thị nội dung tệp, kết hợp nhiều tệp, đánh số dòng
    + Ta có 2 file text chứa nội dung và giờ ta muống gộp nội dung 2 files vào chung 1 file.
```bash
# cat: print 1 or many file
cat -n file.txt
# -n: Đánh số các dòng trong nội dung tệp.
# -b: Đánh số các dòng, nhưng chỉ đối với những dòng không trống.
# -E (end): xem dòng kết thúc, hệ thống sẽ thêm ký hiệu $ vào mỗi cuối dòng.

file1.txt

text from file1

file2.txt

text from file2

$ cat file1.txt file2.txt > file3.txt

file3.txt

text from file1

text from file2
```

- Lệnh cat giúp ta nối file theo vertival (hàng dọc), lệnh join thì ngược lại giúp ta nối file theo horizon (hàng ngang)
- Mặc định join dùng field đầu tiên làm key để ghép 2 file lại với nhau.

```bash
$ join file1.txt file2.txt 
text from file1 from file2
```

- Lệnh join được sử dụng để kết hợp các tệp văn bản theo một cột chung (tương tự như phép kết hợp trong cơ sở dữ liệu). Nó yêu cầu các tệp đầu vào được sắp xếp theo cột sẽ sử dụng để kết hợp.
- Kết hợp hai tệp theo một cột chung.

```file1.txt
1 Alice
2 Bob
3 Charlie
```

```file2.txt
1 Engineer
3 Doctor
2 Artist
```

```bash
join file1.txt file2.txt
1 Alice Engineer
2 Bob Artist
3 Charlie Doctor

#-1 và -2: Chỉ định cột dùng để kết hợp trong các tệp. Mặc định là cột đầu tiên.
join -1 1 -2 1 file1.txt file2.txt
```

- Lệnh paste dùng để nối dòng với dòng, cách nhau bởi TAB, và không gộp chung key như join.

```file1.txt:
Alice
Bob
Charlie
```

```file2.txt
Engineer
Doctor
Artist
```

```bash
paste file1.txt file2.txt

Alice   Engineer
Bob     Doctor
Charlie Artist

# -d: Chỉ định ký tự phân cách giữa các cột (mặc định là tab).
paste -d ',' file1.txt file2.txt

#-s: Nối các dòng theo chiều ngang thay vì theo chiều dọc.
paste -s file1.txt file2.txt

# -: Đọc đầu vào từ stdin (bàn phím).
paste - file2.txt

```

# Hiểu và thành thạo các lệnh ‘tac’, ‘sort’, ‘split’, ‘uniq’, ‘nl’
- tac: in nội dung của một tệp theo thứ tự ngược lại theo chieu doc (từ dòng cuối lên dòng đầu).`

```file.txt
Line 1
Line 2
Line 3
```

```bash
$ tac file.txt

Line 3
Line 2
Line 1
```

- Lệnh sort được sử dụng để sắp xếp các dòng của tệp văn bản theo thứ tự tăng dần hoặc giảm dần, theo một khoá sắp xếp. Khóa sắp xếp mặc định là thứ tự của các ký tự ASCII (theo thứ tự bảng chữ cái).

```bash
# sort <file>	Sắp xếp các dòng trong tệp, theo các ký tự ở đầu mỗi dòng
# sort -r <file>	Sắp xếp các dòng theo thứ tự ngược lại
$ cat file.txt
apple
child
safari
rain
nice
delay
hand
$ sort file.txt
apple
child
delay
hand
nice
rain
safari
$ sort -r file.txt
safari
rain
nice
hand
delay
child
apple
```

-r: Sắp xếp theo thứ tự giảm dần
-n: Sắp xếp số thay vì chuỗi
-k <cột>: Sắp xếp theo cột
-u: Loại bỏ dòng trùng lặp sau khi sắp xếp

```file.txt
banana
apple
cherry
```

```bash
sort file.txt
apple
banana
cherry
```

- Nếu có số:

```bash
10 apple
2 banana
30 cherry

sort -n file.txt

2 banana
10 apple
30 cherry
```

- split : trong Linux được dùng để chia nhỏ một tệp lớn thành nhiều tệp nhỏ hơn theo số dòng hoặc kích thước.
- Lệnh split sử dụng để chia (hoặc tách) một tệp thành các phân đoạn có kích thước bằng nhau để xem và thao tác dễ dàng hơn và thường chỉ được sử dụng trên các tệp tương đối lớn. Theo mặc định, lệnh split tệp thành các phân đoạn 1000 dòng. Tệp gốc không thay đổi và một tập hợp các tệp mới có cùng tên cộng với tiền tố được thêm vào được tạo.

```bash
split -l <số dòng mỗi tệp> <tên_tệp_gốc> <tên_tệp_mới>
```

```file.txt
line 1
line 2
line 3
line 4
line 5
line 6
```

```bash
split -l 2 -d file.txt part_
```

- uniq: Loại bỏ dòng trùng lặp
- Lệnh `uniq yêu cầu các dòng trùng lặp phải liên tiếp, nên chúng ta thường chạy sắp xếp trước, sau đó mới chuyển đầu ra thành uniq`.

```bash
sort file1 file2 | uniq > file3
```

```bash
uniq file.txt
```
-c: Đếm số lần xuất hiện của từng dòng
-d: Chỉ hiển thị các dòng bị trùng lặp
-u: Chỉ hiển thị các dòng không bị trùng lặp

```file.txt
apple
apple
banana
banana
banana
cherry
```

```bash
$ uniq file.txt
apple
banana
cherry
```

```bash
uniq -c file.txt

2 apple
3 banana
1 cherry
```

- Lệnh `nl` giúp đánh số dòng trong tệp.

```file.txt
apple
banana
cherry
```

```bash
nl file.txt

1  apple
2  banana
3  cherry

```

# File-Viewing Commands: ‘head’, ‘tail’, ‘less’, ‘cut’, ‘wc’

- Lệnh head dùng để xem những dòng đầu của tệp tin (theo mặc định là 10 dòng đầu tiên).

```bash
head [tuỳ chọn] file

# -n, --lines=[-]n: In số dòng n đầu tiên của mỗi tệp
# -c, --byte=[-]n: In số byte n đầu tiên của mỗi tệp
# -q: Không in tiêu đề xác định tên tệp
# -v: Luôn in tiêu đề xác định tên tệp
# --help: Hiển thị các trợ giúp
# --version: Thông tin về phiên bản và thoát
```

```bash
$ head -5 /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
# Dùng lệnh head suất ra 20 byte (ký tự) đầu tiên của /etc/passwd
$ head -c 20 /etc/passwd
root:x:0:0:root:/roo
```

- Lệnh tail dùng để xem những dòng đầu của tệp tin (theo mặc định là 10 dòng). Lệnh tail rất hữu ích khi khắc phục sự cố tệp nhật ký.
- **Chú ý**: Lệnh tail -f này rất hữu ích khi dùng để theo dõi trực tiếp các file log.

```bash
# tail [tuỳ chọn] file
tail file.txt

# -n <số dòng>: Hiển thị số dòng cụ thể
# -c <số byte>: Hiển thị số byte cụ thể
# -f: Theo dõi nội dung tệp trực tiếp (hữu ích khi xem log)
```

- Lệnh `less` cho phép xem nội dung tệp theo trang, tiện hơn cat vì có thể cuộn lên/xuống, tìm kiếm.

```bash
less file.txt

# Space	    Xuống trang tiếp theo
# b	        Quay lại trang trước
# q	        Thoát less
# /từ_khóa	Tìm kiếm từ khóa
# n	        Tìm kiếm tiếp theo
```

- Lệnh cut dùng để trích xuất các cột hoặc ký tự cụ thể từ mỗi dòng.

```bash
cut -d '<ký tự phân tách>' -f <cột> file.txt
# or
cut -c <vị trí ký tự> file.txt

# -d '<ký tự>': Định nghĩa ký tự phân tách (mặc định là Tab)
# -f <số cột>: Chọn cột
# -c <số ký tự>: Chọn ký tự
```

```users.txt
id,name,age
1,alice,25
2,bob,30
3,charlie,28
```

```bash
cut -d ',' -f 2 users.txt

name
alice
bob
charlie
```

- wc (word count) dùng để đếm số dòng, từ và ký tự trong tệp.

```bash
wc file.txt

#100  500  4000 file.txt
# 100 → số dòng
# 500 → số từ
# 4000 → số byte
```

# Using ‘grep’, ‘sed’, ‘awk'. Đây là các lệnh phức tạp, chỉ cần nắm cách dùng cơ bản. Các kỹ thuật nâng cao có thể nhờ

- Lệnh `grep` dùng để tìm kiếm văn bản trong tệp hoặc đầu ra của lệnh khác.

```bash
grep [TÙY CHỌN] "chuỗi cần tìm" file.txt

# -i: Không phân biệt hoa/thường
# -v: Hiển thị dòng không chứa chuỗi tìm kiếm
# -n: Hiển thị số dòng chứa chuỗi
# -r: Tìm kiếm đệ quy trong thư mục
# --color=auto: Tô màu kết quả tìm kiếm
```

```bash
# Tìm từ "error" trong file log.txt:
grep "error" log.txt
# Tìm từ "warning" nhưng không phân biệt chữ hoa/thường:
grep -i "warning" log.txt
# Tìm tất cả dòng không chứa "debug":
grep -v "debug" log.txt
```

- Lệnh `sed` (stream editor) tìm kiếm và thay thế, chỉnh sửa dòng trong file hoặc đầu ra của lệnh.
- Sed là một trình chỉnh sửa dòng, thường được sử dụng để thay đổi văn bản trong các tệp

```bash
sed 's/<chuỗi_cũ>/<chuỗi_mới>/g' file.txt
# s = substitute (thay thế)
# g = global (áp dụng cho toàn bộ dòng)

# Thay "Linux" thành "Ubuntu" trong file.txt:
sed 's/Linux/Ubuntu/g' file.txt
# Xóa dòng chứa "error":
sed '/error/d' file.txt
# Chỉ hiển thị dòng 5-10 của tệp:
sed -n '5,10p' file.txt
# Sửa trực tiếp trong file (dùng -i để lưu thay đổi):
sed -i 's/localhost/127.0.0.1/g' config.txt
```

- Lệnh awk cho phép tìm kiếm dữ liệu trong file và in dữ liệu trên console. Nếu một tệp chứa nhiều cột thì cũng có thể tìm thấy dữ liệu trong các cột cụ thể. Hơn nữa, nó còn hỗ trợ các tác vụ như tìm kiếm, thực thi có điều kiện, cập nhật và lọc.
- Lệnh `awk` lấy dữ liệu theo cột, xử lý và tính toán

```bash
# Hiển thị nộii dung toàn bộ file
awk '{print}' test2.txt
# Hiển thị nội dung theo cột
awk '{print $<số_cột>}' file.txt

# $1, $2, $3… là cột 1, 2, 3…
```

```users.txt
1 Alice 25
2 Bob 30
3 Charlie 28
```

```bash
#Lấy cột thứ 2 (tên):
$ awk '{print $2}' users.txt

Alice
Bob
Charlie

# Lấy cột 1 và 3 với dấu - giữa:
$ awk '{print $1 "-" $3}' users.txt

1-25
2-30
3-28

# Tính tổng tuổi:
$ awk '{sum += $3} END {print sum}' users.txt
83

# Lọc dòng có tuổi > 28:
awk '$3 > 28' users.txt
2 Bob 30
```

# Sử dụng thành thạo "vim" để chỉnh sửa nội dung file, nắm các mode trong vim (command mode, ex mode và insert mode)

```bash
vi text.txt
```

```bash
# - insert mode
i	Chèn từ vị trí con trỏ
a	Chèn sau con trỏ
o	Thêm dòng mới bên dưới
I	Chèn từ đầu dòng
A	Chèn cuối dòng
O	Thêm dòng mới bên trên
# Lưu file và thoát
:w	Lưu file
:q	Thoát (nếu không có thay đổi)
:wq hoặc ZZ	Lưu và thoát
:q!	Thoát mà không lưu
# Di chuyển con trỏ
h	Trái
l	Phải
j	Xuống
k	Lên
w	Nhảy tới từ tiếp theo
b	Quay lại từ trước
gg	Lên đầu file
G	Xuống cuối file
# Xóa và cắt/dán
x	Xóa ký tự tại con trỏ
dd	Xóa dòng hiện tại
dw	Xóa một từ
d$	Xóa đến cuối dòng
yy	Copy dòng hiện tại
p	Dán sau con trỏ
#  Tìm kiếm và thay thế
/từ_khóa	Tìm kiếm từ
n	Tới kết quả tiếp theo
N	Quay lại kết quả trước
:%s/old/new/g	Thay thế tất cả old bằng new
```

# Yêu cầu sử dụng thành thạo tmux hoặc byobu ( tạo, đóng windows, đổi tên windows, chuyển đổi qua lại giữa các windows)

- tmux (Terminal Multiplexer) là một công cụ giúp bạn mở nhiều cửa sổ (windows) và phiên làm việc (sessions) trong cùng một terminal. Nó rất hữu ích khi bạn cần quản lý nhiều tác vụ trên server từ xa mà không bị mất kết nối.

```bash
# Tạo một session
tmux new -s my_session
# Hiện thị danh sách các sessions:
tmux ls
# Gắn lại vào session đã thoát
tmux attach -t my_session
# Chuyển giữa các session
tmux switch -t my_session
# Xóa một session
tmux kill-session -t my_session

# Quản lý Windows
Ctrl + d # hoát và đóng luôn session
Ctrl+b c  # Tạo một cửa sổ mới
Ctrl+b w  # Xem danh sách cửa sổ hiện tại
Ctrl+b n/p  #　Chuyển đến cửa sổ tiếp theo hoặc trước đó
Ctrl+b f  #　Tìm kiếm cửa sổ
Ctrl+b ,  #　Đặt/Đổi tên cho cửa sổ
Ctrl+b &  #　Đóng cửa sổ
# 
```

# Sử dụng thành thạo và đọc hiểu các thông số về CPU, RAM, quản lý các tiến trình (process)

- CPU

```bash
$ lscpu      # Hiển thị chi tiết về CPU

# Architecture:             x86_64
#   CPU op-mode(s):         32-bit, 64-bit
#   Address sizes:          45 bits physical, 48 bits virtual
#   Byte Order:             Little Endian

$ cat /proc/cpuinfo  # Xem thông tin từng core CPU
# processor       : 0
# vendor_id       : AuthenticAMD
# cpu family      : 25
# model           : 80
# model name      : AMD Ryzen 5 5625U with Radeon Graphics

$ nproc      # Hiển thị số lượng CPU core
2
```

- RAM

```bash
$ free -h    # Kiểm tra dung lượng RAM còn trống

#                total        used        free      shared  buff/cache   available
# Mem:           3.8Gi       462Mi       2.7Gi       1.5Mi       852Mi       3.3Gi
# Swap:          2.9Gi          0B       2.9Gi

$ free -m
#                total        used        free      shared  buff/cache   available
# Mem:            3868         485        2697           1         927        3382
# Swap:           2924           0        2924

cat /proc/meminfo  # Xem thông tin chi tiết về RAM
```

- Kiểm tra mức độ sử dụng CPU & RAM theo thời gian thực

```bash
# Kiểm tra mức độ sử dụng CPU & RAM theo thời gian thực
top        # Theo dõi tài nguyên CPU, RAM theo thời gian thực
htop       # Giao diện nâng cao của top (cần cài đặt)
```

## Quản lý tiến trình (Processes)

- Xem danh sách các tiến trình đang chạy

```bash
$ ps aux        # Hiển thị tất cả tiến trình đang chạy

# USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
# root           1  0.0  0.3  22420 13484 ?        Ss   04:22   0:05 /sbin/init
# PID (Process ID) – ID của tiến trình
# USER – Người dùng chạy tiến trình
# %CPU – Mức sử dụng CPU
# %MEM – Mức sử dụng RAM

ps -ef        # Hiển thị tiến trình theo định dạng khác
# UID          PID    PPID  C STIME TTY          TIME CMD
# root           1       0  0 04:22 ?        00:00:05 /sbin/init
```

## Dừng hoặc Kill tiến trình

```bash
# Dừng (tạm dừng) một tiến trình:
kill -STOP <PID>  # Dừng tạm thời
kill -CONT <PID>  # Tiếp tục chạy lại
#  Kill một tiến trình:
kill <PID>        # Kill tiến trình theo PID
kill -9 <PID>     # Kill tiến trình mạnh mẽ hơn
pkill <tên_process>  # Kill theo tên tiến trình
```

```bash
# theo dõi một tiến trình cụ thể theo thời gian thực
pidstat -u -p <PID>

$ idstat -u -p 1
Linux 6.8.0-31-generic (ubuntu-22-04)   02/05/2025      _x86_64_        (2 CPU)

06:55:31 AM   UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
06:55:31 AM     0         1    0.01    0.05    0.00    0.02    0.06     0  systemd
```

```bash
ps aux --sort=-%cpu | head -10   # 10 tiến trình tốn CPU nhất
ps aux --sort=-%mem | head -10   # 10 tiến trình tốn RAM nhất

watch -n 1 free -h  # Cập nhật RAM mỗi giây
watch -n 1 "ps aux --sort=-%cpu | head -5"  # Xem tiến trình CPU cao nhất mỗi giây
```

# Foreground và Background Processes trong Linux
- Trong Linux, một tiến trình có thể chạy ở foreground (nền trước) hoặc background (nền sau). Cả hai đều là các process đang chạy trên hệ thống, nhưng chúng có cách hoạt động khác nhau.
## Foreground Process (Tiến trình nền trước)
- Là tiến trình chạy trực tiếp trên terminal và chiếm quyền điều khiển.
- Người dùng phải chờ tiến trình này hoàn thành trước khi có thể nhập lệnh mới.
- Nếu terminal bị đóng, tiến trình sẽ bị dừng hoặc bị giết.

```bash
ping google.com

# Khi chạy lệnh trên:
# Bạn sẽ thấy output liên tục hiển thị trong terminal.
# Muốn dừng tiến trình, nhấn Ctrl + C.
```

## Background Process (Tiến trình nền sau)
- Là tiến trình chạy ẩn, không chiếm quyền điều khiển terminal.
- Người dùng có thể nhập lệnh khác mà không cần chờ tiến trình kết thúc.
- Tiến trình vẫn tiếp tục chạy ngay cả khi terminal bị đóng (nếu dùng nohup hoặc disown).

```bash
# Thêm & vào cuối lệnh:
ping google.com > ping.log &
```

## Khác biệt

```

Đặc điểm	Foreground Process	Background Process
Chiếm terminal	✅ Có	❌ Không
Nhập lệnh khác trong khi chạy	❌ Không	✅ Có
Đóng terminal có ảnh hưởng	✅ Có thể dừng	❌ Không nếu có nohup
Cách chạy	Lệnh thông thường	Lệnh + & hoặc bg
```

# Cách để kill một process, nắm vững các SIGNAL trong kill process để dừng tiến trình
- Kill Process là gì: Trong Linux, khi một chương trình (process) bị treo hoặc cần dừng, ta có thể sử dụng lệnh kill hoặc các công cụ khác để kết thúc nó. Khi kill một tiến trình, ta gửi một tín hiệu (SIGNAL) đến tiến trình đó.

- Trong Linux, SIGNAL là các tín hiệu mà hệ thống gửi đến tiến trình (process) để yêu cầu nó thực hiện một hành động nào đó, chẳng hạn như dừng, tiếp tục, hoặc khởi động lại.

```bash
# kiểm tra danh sách các SIGNAL
$ kill -l
 1) SIGHUP      2) SIGINT      3) SIGQUIT     4) SIGILL  
 5) SIGTRAP     6) SIGABRT     7) SIGBUS      8) SIGFPE  
 9) SIGKILL    10) SIGUSR1    11) SIGSEGV    12) SIGUSR2  
13) SIGPIPE    14) SIGALRM    15) SIGTERM    16) SIGSTKFLT  
17) SIGCHLD    18) SIGCONT    19) SIGSTOP    20) SIGTSTP  
```

```
Các tín hiệu (SIGNAL) quan trọng cần biết
Tín hiệu	Giá trị (Số)	Ý nghĩa	Mô tả
SIGHUP	1	Hangup	Đóng terminal nhưng giữ tiến trình chạy
SIGINT	2	Interrupt	Dừng tiến trình (tương đương Ctrl + C)
SIGQUIT	3	Quit	Dừng tiến trình và tạo core dump
SIGKILL	9	Kill	Dừng tiến trình ngay lập tức, không thể chặn
SIGTERM	15	Terminate	Yêu cầu tiến trình dừng một cách an toàn
SIGSTOP	19	Stop	Tạm dừng tiến trình (tương đương Ctrl + Z)
SIGCONT	18	Continue	Tiếp tục tiến trình sau khi bị tạm dừng
SIGTSTP	20	Terminal Stop	Tạm dừng từ terminal (giống Ctrl + Z)
SIGUSR1	10	User Signal 1	Tín hiệu do người dùng định nghĩa
SIGUSR2	12	User Signal 2	Tín hiệu do người dùng định nghĩa
```

## Cách sử dụng SIGNAL để dừng tiến trình

```bash
# Lệnh kill gửi tín hiệu đến tiến trình dựa vào Process ID (PID):
kill -SIGNAL PID
# kill -9 1234   # Tương đương kill -SIGKILL 1234
kill -15 1234  # Gửi SIGTERM (yêu cầu dừng an toàn)
kill -9 1234   # Gửi SIGKILL (buộc dừng ngay lập tức) # SIGKILL mạnh hơn nhưng không cho tiến trình lưu dữ liệu trước khi dừng.
```

# Manage Software

## Nắm vững khái niệm cơ bản về "package" trong Linux, các khái niệm về dependencies, conflicts, binary packet

### package
- package là tập hợp các file cần thiết để cài đặt, cấu hình, và chạy một chương trình hoặc công cụ nào đó.
Mỗi package thường bao gồm:
✅ Binary files (tệp thực thi)
✅ Configuration files (tệp cấu hình)
✅ Dependencies (thư viện cần thiết)
✅ Documentation (hướng dẫn sử dụng)

- Package Manager trong Linux: Mỗi hệ điều hành Linux có trình quản lý gói (package manager) riêng để cài đặt, cập nhật và gỡ bỏ phần mềm

```bash
apt install nginx   # Ubuntu/Debian
```

### Dependencies
- Một package có thể cần các thư viện hoặc chương trình khác để chạy. Đây gọi là dependencies.

```bash
# Khi cài đặt nginx, nó cần một số thư viện như libpcre3, zlib1g, libssl1.1., Nếu thiếu dependencies, có thể lỗi
apt show nginx | grep Depends
```

### Conflicts

- Khi hai package có cùng file hoặc phụ thuộc không tương thích, xảy ra conflict (xung đột).
- Ví dụ: cài nginx nhưng đã có apache2, cả hai sử dụng cổng 80, gây xung đột.

```bash
apt remove apache2 # Gỡ bỏ package xung đột
apt show nginx | grep Conflicts # Kiểm tra trước khi cài
```

### Binary Package
- Binary Package là gói phần mềm chứa file nhị phân đã biên dịch sẵn, giúp ta cài đặt nhanh chóng mà không cần biên dịch mã nguồn
```
Hệ điều hành	Định dạng gói nhị phân	Trình quản lý gói
Debian, Ubuntu	.deb	                dpkg, apt
```

## Học cách dùng các chương trình nén trong Linux, "tar", "zip", "unrar", , "gunzip"
- tar: dùng để nén và giải nén các file và thư mục.
```bash
tar [options] -f archive.tar [file1] [file2] [directory]

# -c : Tạo file nén
# -x : Giải nén
# -v : Hiển thị tiến trình
# -f : Chỉ định tên file
# -z : Nén với gzip (.tar.gz)
# -j : Nén với bzip2 (.tar.bz2)

$ tar -cvf archive.tar file1.txt file2.txt 
$ tar -xvf archive.tar
```
- zip

```bash
zip [options] archive.zip file1 file2 directory
# -r : Nén thư mục và toàn bộ nội dung
# Nén
zip -r archive.zip folder/
# Giải nén
unzip archive.zip
```

- unrar - Giải nén .rar

```bash
$ unrar [options] archive.rar
# x : Giải nén toàn bộ
# l : Liệt kê nội dung file .rar
$ unrar x archive.rar
$ unrar l archive.rar # Liệt kê nội dung
```

- gzip/gunzip - Nén và giải nén file đơn

```bash
gzip [options] file
gunzip [options] file.gz
```

## Thành thạo cách dùng Debian package để quản lý (dpkg, apt-get), cài đặt phần mềm trên Ubuntu
Trên Ubuntu (hoặc Debian), có hai công cụ chính để quản lý gói phần mềm:
+ dpkg: Dùng để quản lý các gói .deb cục bộ.
+ apt-get / apt: Quản lý gói thông qua kho lưu trữ trực tuyến.

```bash
wget http://archive.ubuntu.com/ubuntu/pool/universe/c/cowsay/cowsay_3.03+dfsg2-4_all.deb
# Install Package
sudo dpkg -i <package.deb>
sudo dpkg -i cowsay_3.03+dfsg2-4_all.deb
# Liệt kê tất cả các gói đã cài
dpkg -l <package>
dpkg -l cowsay
# Remove Package
dpkg -r <package>
sudo dpkg -r cowsay
```

```bash
apt update
apt install nginx
apt remove nginx
sudo apt-get purge nginx #gỡ bỏ cả dữ liệu cấu hình

# Nâng cấp phần mềm
apt upgrade
```

- Khác biệt giữa dpkg và apt-get

```
Công cụ	Chức năng
dpkg	Cài đặt gói .deb cục bộ, không tự động xử lý phụ thuộc
apt-get / apt	Cài đặt gói từ kho chính thức, tự động giải quyết phụ thuộc
```

## Tìm hiểu về StartUp Script, cách để các chương trình khởi động khi boot server

### StartUp Script - Khởi động chương trình khi server khởi động
- Sử dụng systemd (Dịch vụ systemctl) – Phù hợp cho Ubuntu 
- Sử dụng systemd để tạo service tự động chạy khi boot

```bash
vi /etc/systemd/system/myapp.service

[Unit]
Description=My Startup App
After=network.target

[Service]
ExecStart=/usr/bin/python3 /home/user/myscript.py
WorkingDirectory=/home/user/
User=user
Restart=always

[Install]
WantedBy=multi-user.target
```

Giải thích:
 + [Unit] - Thông tin chung về service
 + After=network.target: Đảm bảo rằng dịch vụ này chỉ chạy sau khi mạng đã sẵn sàng. Điều này rất quan trọng nếu chương trình cần kết nối internet hoặc một cơ sở dữ liệu từ xa.
 + [Service] - Cấu hình cách service hoạt động
 + ExecStart: Lệnh để chạy ứng dụng.
 + Restart=always: Tự động restart nếu bị crash.
 + [Install] - Cấu hình để tự động chạy khi boot
 + WantedBy=multi-user.target: Xác định khi nào service sẽ khởi động (Điều này có nghĩa là service sẽ tự động chạy khi hệ thống khởi động vào chế độ "multi-user" (tức là khi server sẵn sàng hoạt động mà không cần GUI))

```bash
systemctl daemon-reload
systemctl enable myapp.service
systemctl start myapp.service
systemctl status myapp.service
systemctl stop myapp.service
systemctl restart myapp.service
```

### Tìm hiểu cách cài đặt một chương trình từ Source, gồm các bước gì

```bash
apt update
apt install nginx
systemctl start nginx
systemctl enable nginx
```


# Managing Files and Filesystems

## Hiểu biết về các loại File System
- File system được dùng để quản lý cách dữ liệu được đọc và lưu trên thiết bị.
- File system cho phép người dùng truy cập nhanh chóng và an toàn khi cần thiết.
- Các loại filesystem được Linux hỗ trợ:
    Filesystem cơ bản: EXT2, EXT3, EXT4, XFS, Btrfs, JFS, NTFS,…
    Filesystem dành cho dạng lưu trữ Flash: thẻ nhớ,…
    Filesystem dành cho hệ cơ sở dữ liệu
    Filesystem mục đích đặc biệt: procfs, sysfs, tmpfs, squashfs, debugfs,…
- Một phân vùng là một vùng chứa trong đó có một filesystem được lưu trữ , trong một số trường hợp thì filesystem có thể mở rộng hơn một phân vùng nếu filesystem sử dụng các liên kết.
- Linux dùng ký tự ‘/’ để tách các đường dẫn ( khác với Windows sử dụng “\” để tách các đường dẫn) tất cả các tập tin thư mục điều được bắt đầu từ thư mục gốc ( / ), cũng không có kí tự ổ đĩa giống như Windows.

1. Phân vùng (Partition):
    + Định nghĩa: Phân vùng là một phần của ổ đĩa vật lý được phân chia ra để hệ thống có thể quản lý và sử dụng. Mỗi phân vùng có thể chứa một hệ thống tệp hoặc không.
    + Chức năng: Phân vùng giúp chia nhỏ ổ đĩa thành các đơn vị nhỏ hơn, dễ dàng quản lý. Nó có thể được định dạng và sử dụng cho các mục đích khác nhau như lưu trữ hệ điều hành, dữ liệu người dùng, hoặc phân vùng swap.
    + Cấu trúc: Một ổ đĩa có thể có nhiều phân vùng. Ví dụ, bạn có thể chia ổ đĩa 500GB thành 3 phân vùng: một phân vùng cho hệ điều hành, một phân vùng cho dữ liệu, và một phân vùng swap.
    + Ví dụ: /dev/sda1, /dev/sda2 là các phân vùng trong Linux, nơi /dev/sda1 có thể là phân vùng chứa hệ điều hành, trong khi /dev/sda2 có thể là phân vùng lưu trữ dữ liệu.
2. Hệ thống tệp (Filesystem):
    + Định nghĩa: Hệ thống tệp là cách mà dữ liệu được tổ chức và lưu trữ trên một phân vùng hoặc thiết bị lưu trữ. Nó xác định cách các tệp và thư mục được lưu trữ, quản lý, và truy cập trong phân vùng đó.
    + Chức năng: Hệ thống tệp giúp hệ điều hành tổ chức dữ liệu trên phân vùng sao cho việc lưu trữ và truy xuất dữ liệu trở nên hiệu quả. Nó quyết định cách thức các tệp tin được đặt tên, tổ chức trong thư mục, cách quản lý quyền truy cập, và nhiều yếu tố khác.
    + Cấu trúc: Hệ thống tệp có thể có các kiểu khác nhau như ext4, ntfs, xfs, btrfs, v.v. Mỗi loại hệ thống tệp có các đặc điểm và ưu điểm riêng (như hiệu suất, khả năng phục hồi dữ liệu, tính bảo mật).
    + Ví dụ: ext4, ntfs, btrfs là các loại hệ thống tệp được sử dụng trong Linux và các hệ điều hành khác. Một phân vùng có thể được định dạng bằng một hệ thống tệp cụ thể.

Ví dụ: 1TB. Bạn chia ổ đĩa này thành 2 phân vùng: /dev/sda1 và /dev/sda2.
Trên phân vùng /dev/sda1, bạn có thể định dạng hệ thống tệp ext4 để chứa hệ điều hành.
Trên phân vùng /dev/sda2, bạn có thể định dạng hệ thống tệp ntfs để chứa dữ liệu và chia sẻ với Windows.

- ext: Là một sự mở rộng của hệ thống tệp minix. Nó đã được thay thế hoàn toàn bởi phiên bản thứ hai của hệ thống tệp mở rộng (ext2) và đã bị loại bỏ khỏi kernel (trong Linux 2.1.21).
- ext2: Là một hệ thống tệp đĩa được Linux sử dụng cho cả đĩa cố định và phương tiện di động. Đây là hệ thống tệp mở rộng thứ hai được thiết kế như một sự mở rộng của hệ thống tệp ext. Xem ext2(5).
- ext3: Là một phiên bản journaling của hệ thống tệp ext2. Rất dễ dàng để chuyển đổi qua lại giữa ext2 và ext3. Xem ext3(5).
- ext4: Là một bộ nâng cấp cho ext3 bao gồm những cải tiến đáng kể về hiệu suất và độ tin cậy, cộng với việc tăng lớn giới hạn dung lượng, kích thước tệp và thư mục. Xem ext4(5).
- ntfs: Là hệ thống tệp gốc của Microsoft Windows NT, hỗ trợ các tính năng như ACLs, journaling, mã hóa, v.v.

## Cách kiểm tra và sửa lỗi file system - fsck

```bash
# Kiểm tra phân vùng đã được gắn kết hay chưa
fsck [options] <filesystem>
# Ví dụ, để kiểm tra phân vùng /dev/sda1, bạn có thể chạy:
fsck -y /dev/sda1
# Kiểm tra tất cả các hệ thống tệp trong
sudo fsck -A
# Kiểm tra tất cả các hệ thống tệp và hiển thị thanh tiến độ
sudo fsck -AC
```

## Cách kiểm tra dung lượng disk, sử dụng 2 lệnh "df" và "du" cùng các option. Tự tìm tài liệu và đọc kỹ.

- Lệnh df hiển thị thông tin về dung lượng ổ đĩa và sử dụng của các phân vùng.
- Lệnh “df” viết tắt của “disk filesystem“, nó được dùng để lấy toàn bộ thông tin về lượng ổ cứng khả dụng và lượng ổ cứng đã dùng của các file hệ thống trên linux. 

```bash
# Kiểm tra dung lượng của tất cả các phân vùng:
$ df
# Hiển thị dung lượng bằng đơn vị dễ đọc 
df -h # humman
# Hiển thị chỉ dung lượng của phân vùng gốc (/):
df -h /
# Kiểm tra kích thước và phần trăm đĩa đã sử dụng, 
df -h --output='size','pcent' /
```

- Lệnh du giúp bạn kiểm tra dung lượng sử dụng của các thư mục và tệp.
- du là một công cụ dòng lệnh được cung cấp bởi Linux, nhằm báo cáo dung lượng ổ đĩa được sử dụng bởi các thư mục và file. du là viết tắt của từ “disk usage”. 

```bash
# Kiểm tra dung lượng của thư mục và hiển thị kết quả theo đơn vị dễ đọc:
du -h
# Kiểm tra dung lượng của tất cả các thư mục con trong thư mục hiện tại:
du -h --max-depth=1 #  độ xâu
# Hiển thị tổng dung lượng của thư mục:
du -sh /path/to/directory # -s: Chỉ hiển thị tổng dung lượng mà không liệt kê các thư mục con.
```

## Tạo file system bằng lệnh mkfs

- Lệnh mkfs (viết tắt của make filesystem) trên Linux được sử dụng để tạo một filesystem trên một phân vùng hoặc thiết bị lưu trữ 

```bash
#format partition
sudo mkfs -t ext4 /dev/sdb1
#Mount
sudo mkdir /data
sudo mount /dev/sdb1 /data
```

#  ‘mount’, ‘umount’

- Lệnh `mount` được sử dụng để gắn kết một filesystem vào cây thư mục của hệ thống, cho phép bạn truy cập và sử dụng dữ liệu trên filesystem đó.

```bash
mount <thiết_bị> <thư_mục_mount>
# <thiết_bị>: Tên thiết bị hoặc phân vùng (ví dụ: /dev/sdb1, /dev/sdc, v.v.).
# <thư_mục_mount>: Thư mục trên hệ thống mà bạn muốn gắn kết filesystem vào

mount /dev/sdb1 /mnt/mydrive

mount -t ext4 /dev/sdb1 /mnt/mydrive

# Mount tất cả filesystem được định nghĩa trong /etc/fstab
sudo mount -a
# Xem danh sách các filesystem đã được mount
mount 
df -h
```

- Lệnh umount được sử dụng để ngắt kết nối một filesystem khỏi hệ thống. Điều này đảm bảo rằng mọi dữ liệu đang được ghi sẽ được đồng bộ và filesystem được đóng đúng cách.

```bash
# Unmount thư mục /mnt/mydriv
sudo umount /mnt/mydata
```

**Lưu ý:** không umount khi filesystem đang được sử dụng. Sử dụng lệnh lsof hoặc fuser để kiểm tra tiến trình đang sử dụng filesystem:

```bash
lsof /mnt/mydrive
```

- Tự động mount khi khởi động: `/etc/fstab`
- File /etc/fstab chứa thông tin về các filesystem sẽ được mount tự động khi hệ thống khởi động.

```bash
/dev/sdb1  /mnt/mydrive  ext4  defaults  0  2
```

# Nắm khai niệm về Partition của một đĩa

- Partition (phân vùng) là cách chia ổ đĩa thành các phần riêng biệt để tổ chức dữ liệu tốt hơn và quản lý hệ thống hiệu quả. Trên Linux, ổ đĩa thường được chia thành nhiều partition với các mục đích khác nhau.

- Mục đích của Partition:
    + Tách biệt hệ điều hành và dữ liệu người dùng: Ví dụ, có thể cài hệ điều hành trên phân vùng root (/) và lưu trữ dữ liệu người dùng trên phân vùng /home.
    + Dễ dàng quản lý, sao lưu và phục hồi: Nếu hệ điều hành gặp sự cố, dữ liệu trong các partition riêng biệt (như /home) thường không bị ảnh hưởng.
    + Phân chia riêng các phân vùng cho các loại dữ liệu khác nhau (ví dụ: /var chứa log files) giúp cải thiện hiệu năng và tránh trường hợp một phân vùng đầy dữ liệu làm ảnh hưởng đến toàn bộ hệ thống.
    + Tách biệt các vùng dữ liệu có thể giúp quản lý quyền truy cập và bảo vệ hệ thống khỏi các lỗi hay tấn công không mong muốn.

## Các loại partition cơ bản,

- Primary Partition (Phân vùng chính):
    + Có thể chứa hệ điều hành hoặc dữ liệu.
    + Một ổ đĩa có tối đa 4 primary partitions hoặc 3 primary + 1 extended partition.
- Extended Partition:
    + Không thể chứa dữ liệu trực tiếp.
    + Được sử dụng để tạo nhiều logical partition.
- Logical Partition:
    + Nằm bên trong extended partition.
    + Có thể tạo nhiều hơn 4 partition bằng cách sử dụng logical partitions.
    + Thường được sử dụng để lưu trữ dữ liệu, hoặc cho các mục đích phụ như phân vùng swap, phân vùng chứa dữ liệu người dùng,...
    + Không thể đánh dấu trực tiếp là phân vùng khởi động (bootable) trong một số trường hợp, mặc dù vẫn có thể khởi động hệ điều hành từ chúng thông qua các phương pháp đặc biệt hoặc cấu hình boot loader.


## Tạo/Quản lý Partition đĩa bằng ‘fdisk’. 

- fdisk là tiện ích quản lý phân vùng đĩa cứng trên Linux. Sử dụng fdisk, bạn có thể xem, tạo, thay đổi kích thước, xóa, thay đổi, sao chép và di chuyển các phân vùng.
- fdisk cho phép tạo tối đa bốn phân vùng chính được Linux cho phép với mỗi phân vùng yêu cầu kích thước tối thiểu 40mb.

```bash
# xem tất cả phân vùng trên một ổ đãi
fdisk -l /dev/sda

fdisk -l

root@ubuntu-22-04:~# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sdb                         8:16   0   20G  0 disk 

root@ubuntu-22-04:~# fdisk -l /dev/sdb
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VMware Virtual S

#B1
root@ubuntu-22-04:~# fdisk /dev/sdb
Command (m for help): 
#   n  Tạo phân vùng mới
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)

#B2 Nhập n để tạo phân vùng mới sẽ nhắc bạn chỉ định cho một phân vùng chính hoặc phân vùng mở rộng. Nhập p cho phân vùng chính hoặc e cho phân vùng mở rộng.

Select (default p): p
#B3 Sau đó, bạn sẽ được nhắc nhập số của phân vùng sẽ được tạo. Bạn có thể nhấn Enter để chấp nhận mặc định.
Partition number (1-4, default 1): 1

#B4 Sau đó, bạn sẽ được nhắc nhập kích thước của phân vùng sẽ được tạo ví dụ dưới chúng ta sẽ tạo đĩa với size 5GB. 
First sector (2048-41943039, default 2048): 2048
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-41943039, default 41943039): 10002400

Created a new partition 1 of type 'Linux' and of size 4.8 GiB.
# Bước 5: Chạy lệnh w để lưu các thay đổi.
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
# Tương tự chúng ta sẽ tạo phân vùng khác /dev/sdb2.
# Bước 6: Kiểm tra xem có phân vùng mới chưa.

root@ubuntu-22-04:~# fdisk -l /dev/sdb
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Device     Boot Start      End  Sectors  Size Id Type
/dev/sdb1        2048 10002400 10000353  4.8G 83 Linux

# Bước 7: Chúng ta có hai phân vùng mới được tạo. Sau khi tạo phân vùng phải thông báo cho hệ điều hành để cập nhật bảng phân vùng.
root@ubuntu-22-04:~# partprobe /dev/sdb

# Bước 8: Bảng phân vùng đã được cập nhật. Chúng ta phải định dạng phân vùng của để sử dụng. Hệ thống định dạng tệp được hỗ trợ Linux là xfs. Lệnh sau để định dạng phân vùng sdb1 với xfs.
root@ubuntu-22-04:~# mkfs.xfs /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=312511 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=1
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=1250044, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

# Bước 9: Sau khi định dạng thành công phân vùng mới các phân vùng đã có thể lưu trữ dữ liệu. Chúng ta phải gắn phân vùng vào thư mục. Tạo các thư mục này bằng lệnh sau:

mkdir /data

# Bước 10: Gắn /etc/sdb1 vào /data
mount /dev/sdb1 /data

root@ubuntu-22-04:~# df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              387M  1.5M  386M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   18G  5.6G   12G  33% /
tmpfs                              1.9G     0  1.9G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          1.8G  182M  1.5G  12% /boot
tmpfs                              387M   12K  387M   1% /run/user/0
/dev/sdb1                          4.8G  125M  4.6G   3% /data

# Bước 11: Các phân vùng gắn kết của chúng ta là tạm thời. Nếu hệ điều hành được khởi động lại, các thư mục được gắn kết này sẽ bị mất. Vì vậy, chúng ta cần phải gắn kết vĩnh viễn. Để thực hiện gắn kết vĩnh viễn phải nhập trong tệp /etc/fstab. 
root@ubuntu-22-04:~# vi /etc/fstab
/dev/sdb1  /data  ext4  defaults  0  0
# Lưu lại file /etc/fstab và chạy lệnh:
mount -a
```

```bash
# Bước 1: Khi xóa bất kỳ phân vùng nào, chúng ta phải ngắt kết nối phân vùng và xóa mục nhập /etc/fstab
# Bước 2: Ngắt kết nối thư mục /data được gắn vào phân vùng sdb1
umount /data
# Bước 3: Xem các phân vùng đĩa hiện có, sau đó chạy lệnh fdisk /dev/sdb
fdisk -l /dev/sdb
fdisk /dev/sdb
# Bước 4: Nhập d để xóa một phân vùng. Bạn sẽ được nhắc nhở để nhập số phân vùng trường hợp này chúng ta nhập 1 để xóa /dev/sdb1. Lưu các thay đổi bằng cách nhập w.
root@ubuntu-22-04:~# fdisk -l /dev/sdb
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VMware Virtual S
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xe06a0d71
```

# Tìm hiểu các khái niệm và cấu trúc của RAID, nguyên lý hoạt động, mdadm để quản lý RAID trong Linux

- RAID là từ viết tắt của cụm từ "Redundant Array of Inexpensive Disks" hoặc "Redundant Arrays of Independent Disks", là hình thức ghép nhiều ổ đĩa vật lý lại với nhau nhằm dung lỗi và tăng tốc.

- RAID là một tập hợp các đĩa trong một nhóm để trở thành một logical volume. RAID chứa các nhóm hoặc bộ (set) hoặc mảng (array). Số đĩa tối thiểu 2 được kết nối với một bộ điều khiển (controller) RAID và tạo ra một logical volume hoặc nhiều ổ đĩa hơn có thể nằm trong một nhóm. Chỉ có một cấp độ RAID có thể được áp dụng trong một nhóm các đĩa. RAID được sử dụng khi chúng ta cần hiệu suất tuyệt vời. Theo cấp độ RAID đã chọn của chúng ta, hiệu suất sẽ khác nhau. Lưu dữ liệu của chúng ta bằng khả năng chịu lỗi và tính sẵn sàng cao.

- Lợi thế của việc sử dụng RAID:
    + Dự phòng
    + Hiệu quả cao
    + Giá thành thấp

## Các khái niệm về RAID
    + Parity là một phương pháp tạo lại nội dung bị mất từ ​​thông tin parity. Chúng được ứng dụng trong RAID5 và RAID6 dựa trên parity.
    + Stripe chia sẻ dữ liệu ngẫu nhiên vào nhiều đĩa. Việc chia sẻ dữ liệu này thì sẽ không có dữ liệu đầy đủ trong một đĩa.
    + Mirroring được sử dụng trong RAID1 và RAID10. Mirroring tạo một bản sao của cùng một dữ liệu. Trong RAID 1, nó sẽ lưu cùng một nội dung vào đĩa khác.
    + Hot spare là một ổ đĩa dự phòng trong máy chủ nó có thể tự động thay thế các ổ đĩa bị lỗi. Nếu bất kỳ một trong các ổ đĩa bị lỗi, ổ đĩa dự phòng này sẽ được sử dụng và xây dựng lại tự động.
    + Chunks là một kích thước của dữ liệu có thể tối thiểu từ 4KB trở lên. Bằng cách xác định kích thước chunks, chúng ta có thể tăng hiệu suất I/O.
    + RAID0 (Striping) dữ liệu sẽ được ghi vào đĩa bằng phương pháp chia sẻ, một nửa nội dung sẽ nằm trong một đĩa và một nửa khác sẽ được ghi vào đĩa khác. RAID0 cho phép ghi và đọc được hoàn thành nhanh hơn. Tuy nhiên, không giống như các cấp RAID khác, RAID0 không có tính Parity. Khi phân chia đĩa mà không có dữ liệu parity không có dự phòng hoặc khả năng chịu lỗi. Nếu một ổ đĩa bị lỗi, tất cả dữ liệu trên ổ đĩa đó sẽ bị mất.
## RAID0
- RAID0 (Striping) dữ liệu sẽ được ghi vào đĩa bằng phương pháp chia sẻ, một nửa nội dung sẽ nằm trong một đĩa và một nửa khác sẽ được ghi vào đĩa khác. RAID0 cho phép ghi và đọc được hoàn thành nhanh hơn. Tuy nhiên, không giống như các cấp RAID khác, RAID0 không có tính Parity. Khi phân chia đĩa mà không có dữ liệu parity không có dự phòng hoặc khả năng chịu lỗi. Nếu một ổ đĩa bị lỗi, tất cả dữ liệu trên ổ đĩa đó sẽ bị mất.

- RAID0 được sử dụng tốt nhất để lưu trữ văn bản có yêu cầu đọc và ghi tốc độ cao. Bộ nhớ đệm phát trực tiếp video và chỉnh sửa video là những cách sử dụng phổ biến cho RAID 0 do tốc độ và hiệu suất.

- Ưu điểm của RAID0 là cải thiện hiệu năng. RAID0 tránh tình trạng nghe lỏm bằng cách không sử dụng dữ liệu parity và bằng cách sử dụng tất cả dung lượng lưu trữ dữ liệu có sẵn. RAID0 có chi phí thấp nhất trong tất cả các cấp RAID và được hỗ trợ bởi tất cả các bộ điều khiển phần cứng.

- Nhược điểm của RAID0 là khả năng phục hồi thấp. Nó không nên được sử dụng để lưu trữ quan trọng.

## RAID1
- RAID1 (Mirroring) là sự sao chép dữ liệu vào hai hoặc nhiều đĩa. RAID1 là một lựa chọn tốt cho các ứng dụng đòi hỏi hiệu năng cao và tính sẵn sàng cao, như các ứng dụng giao dịch, email,... Cả hai đĩa đều hoạt động, dữ liệu có thể được đọc từ chúng đồng thời làm cho hoạt động đọc khá nhanh. Tuy nhiên, thao tác ghi chậm hơn vì thao tác ghi được thực hiện hai lần.
- RAID1 yêu cầu tối thiểu hai đĩa vật lý, vì dữ liệu được ghi đồng thời đến hai nơi. Nếu một đĩa bị lỗi, đĩa kia có thể truy xuất dữ liệu.

## RAID5
- RAID5 (Distributed Parity) được sử dụng ở cấp doanh nghiệp. RAID5 hoạt động theo phương pháp parity. Thông tin chẵn lẻ sẽ được sử dụng để xây dựng lại dữ liệu. Nó xây dựng lại từ thông tin còn lại trên các ổ đĩa tốt còn lại. Điều này sẽ bảo vệ dữ liệu của chúng ta khi ổ đĩa bị lỗi. Dử liệu trên RAID5 có thể tồn tại sau một lỗi ổ đĩa duy nhất, nếu các ổ đĩa bị lỗi nhiều hơn 1 sẽ gây mất dữ liệu.

- Một số ưu điểm của RAID5:
    + Đọc sẽ cực kỳ rất tốt về tốc độ.
    + Ghi sẽ ở mức trung bình, chậm nếu chúng tôi không sử dụng card điều khiển RAID.
    + Xây dựng lại từ thông tin parity từ tất cả các ổ đĩa.
RAID5 Có thể được sử dụng trong các máy chủ tập tin, máy chủ web, sao lưu rất quan trọng.

## RAID6
- RAID6 (Two Parity Distributed Disk) giống như RAID5 hoạt động theo phương pháp parity. Chủ yếu được sử dụng trong một số lượng lớn các mảng. Chúng ta cần tối thiểu 4 ổ đĩa, khi có 2 ổ đĩa bị lỗi, chúng ta có thể xây dựng lại dữ liệu trong khi thay thế các ổ đĩa mới.
- Rất chậm so với RAID5, vì nó ghi dữ liệu cho cả 4 trình điều khiển cùng một lúc. Sẽ có tốc độ trung bình trong khi chúng ta sử dụng card điều khiển RAID. Nếu chúng ta có 6 số ổ cứng 1TB thì 4 ổ đĩa sẽ được sử dụng cho dữ liệu và 2 ổ đĩa sẽ được sử dụng cho parity.
- Ưu và nhược điểm của RAID:
    + Hiệu suất kém.
    + Hiệu suất đọc sẽ tốt.
    + Hiệu suất ghi sẽ kém nếu chúng tôi không sử dụng card điều khiển RAID.
    + Xây dựng lại từ 2 ổ đĩa parity.
    + Không gian 2 đĩa sẽ nằm dưới parity.
    + Có thể được sử dụng trong mảng lớn.
    + Có thể được sử dụng trong mục đích sao lưu, truyền phát video, được sử dụng ở quy mô lớn.

## RAID10
- RAID10 có thể được gọi là RAID1 + RAID0 hoặc RAID0 + RAID1. RAID10 sẽ làm cả hai công việc của Mirror và Striping. Mirror sẽ là đầu tiên và Stripe sẽ là thứ hai trong RAID10. Stripe sẽ là đầu tiên và mirror sẽ là thứ hai trong RAID01. RAID10 tốt hơn so với RAID01.
- Ưu và nhược điểm của RAID10:
    + Hiệu suất đọc và viết tốt.
    + Nửa không gian sẽ bị mất trong tổng công suất.
    + Xây dựng lại nhanh chóng từ sao chép dữ liệu.
    + Có thể được sử dụng trong lưu trữ cơ sở dữ liệu cho hiệu suất cao và tính sẵn sàng.

##  mdadm
- RAID được quản lý bằng gói mdadm trong hầu hết các bản phân phối Linux. Để kiểm tra xem gói mdadm có khả dụng trên hệ thống của chưa bằng cách chạy lệnh sau:

## Tạo RAID0
- Khi sử dụng RAID0 nếu chúng ta có hai đĩa và lưu nội dung vào logical volume thì nó sẽ được lưu vào cả hai đĩa vật lý bằng cách chia nội dung. RAID0 sẽ không thể lấy dữ liệu nếu một trong các ổ đĩa bị lỗi.
- Ưu điểm của RAID0:
    + RAID 0 có hiệu năng cao.
    + Không có dung lượng sẽ bị lãng phí.
    + Zero Fault Tolerance (Không thể lấy lại dữ liệu nếu bất kỳ một trong các đĩa bị lỗi).
- Trong ví dụ dưới đây yêu cầu: Số lượng đĩa ít nhất để tạo RAID0 là 2 bạn có thể thêm nhiều đĩa hơn nhưng thứ tự sẽ gấp đôi 2, 4, 6, 8. 

```bash
# Bước 1: Cập nhật hệ thống và cài đặt mdadm để quản lý RAID
# Bước 2: Kiểm tra thông tin ổ đĩa trên máy:
# Trước khi tạo RAID0, cần đảm bảo có ít nhất hai ổ đĩa cứng chạy lệnh sau để kiểm tra:
root@ubuntu-22-04:~# fdisk -l |grep sd
Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
/dev/sda1     2048     4095     2048    1M BIOS boot
/dev/sda2     4096  3719167  3715072  1.8G Linux filesystem
/dev/sda3  3719168 41940991 38221824 18.2G Linux filesystem
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk /dev/sdc: 20 GiB, 21474836480 bytes, 41943040 sectors
# kiểm tra xem các ổ cứng có sử dụng RAID nào chưa bằng lệnh mdadm cùng với tùy chọn --examine như bên dưới.
root@ubuntu-22-04:~# mdadm --examine /dev/sd[b-c]
mdadm: No md superblock detected on /dev/sdb.
mdadm: No md superblock detected on /dev/sdc.
# Kết trên trả về cho chúng ta biết rằng không có RAID nào được áp dụng cho hai ổ sdb và sdc.
#  Bước 3: Tạo phân vùng đĩa cứng
# Thực hiện tạo phân vùng trên đĩa có tên là sdb và sdc cho RAID bằng lệnh fdisk.
root@ubuntu-22-04:~# fdisk -l | grep sd
Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
/dev/sda1     2048     4095     2048    1M BIOS boot
/dev/sda2     4096  3719167  3715072  1.8G Linux filesystem
/dev/sda3  3719168 41940991 38221824 18.2G Linux filesystem
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
/dev/sdb1        2048 10002400 10000353  4.8G 83 Linux
Disk /dev/sdc: 20 GiB, 21474836480 bytes, 41943040 sectors
/dev/sdc1        2048 10002400 10000353  4.8G 83 Linux
# Tiếp theo chúng ta chạy lệnh bên dưới để kiểm tra xem các đĩa hiện có tham gia RAID nào không:
#  Bước 4: Tạo RAID0
mdadm -C /dev/md0 -l raid0 -n 2 /dev/sd[b-c]1
root@ubuntu-22-04:~# mdadm --create /dev/md0 --level=stripe --raid-devices=2 /dev/sd[b-c]1
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
# -C: Tạo RAID mới.
# -l: Level của RAID.
# -n: Không có thiết bị RAID.
# Kiểm tra lại RAID vừa tạo bằng các cách sau:
Personalities : [raid0] [raid1] [raid6] [raid5] [raid4] [raid10]
md0 : active raid0 sdc1[1] sdb1[0]
      9989120 blocks super 1.2 512k chunks

unused devices: <none>
# Qua kết quả trên cho chúng ta thấy RAID0 đã được tạo với hai phân vùng sdb1 và sdc1.
# Kiểm tra chi tiết
mdadm -E /dev/sd[b-c]1
# Bước 5: Tạo file system (ext4) cho thiết bị RAID /dev/md0
root@ubuntu-22-04:~# mkfs.ext4 /dev/md0
mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 2497280 4k blocks and 624624 inodes
Filesystem UUID: c9d0f418-ac1e-496a-9b2c-19ca1f36f7a4
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done 
# Tiếp theo tạo thư mục gắn kết /data để gắn thiết bị /dev/md0 thực hiện như bên dưới
root@ubuntu-22-04:~# mount /dev/md0 /data
# Kiểm tra mount thành công hay chưa
oot@ubuntu-22-04:~# df -h 
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              387M  1.5M  386M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   18G  5.6G   12G  33% /
tmpfs                              1.9G     0  1.9G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          1.8G  182M  1.5G  12% /boot
tmpfs                              387M   12K  387M   1% /run/user/0
/dev/md0                           9.3G   24K  8.8G   1% /data
```

# Khái niệm về LVM, PG, PV. Các thao tác để tạo, xóa, reszie LVM
- Logical Volume Management(LVM) dùng quản lí các thiết bị lưu trữ .LVM là một tiện ích cho phép chia không gian đĩa cứng thành những Logical Volume từ đó giúp cho việc thay đổi kích thước trở nên dễ dàng.

- Ưu điểm của LVM là tăng tính linh hoạt và khả năng kiểm soát. Các tập có thể được thay đổi kích thước động khi yêu cầu không gian thay đổi và di chuyển giữa các thiết bị vật lý trong nhóm trên hệ thống đang chạy hoặc xuất dễ dàng. LVM cũng cung cấp các tính năng nâng cao như Backup hệ thống bằng cách snapshot các phân vùng ổ cứng(real-time), Migrate dữ liệu dễ dàng.

- LVM hiện có 2 phiên bản cho hệ điều hành Linux:
    + LVM 1: Phiên bản nằm trong kernel 2.4 series
    + LVM 2: Phiên bản mới nhất và lớn nhất của LVM cho Linux. LVM 2 sử dụng trình điều khiển kernel mapper.

# Nắm các cấu trúc File System trong Linux, các thư mục để làm gì, chứa những gì.

1. / - Root
- Đúng với tên gọi của mình: nút gốc (root) đây là nơi bắt đầu của tất cả các file và thư mục. Chỉ có root user mới có quyền ghi trong thư mục này. Chú ý rằng /root là thư mục home của root user chứ không phải là /.

2. /bin - Chương trình của người dùng
- Thư mục này chứa các chương trình thực thi. Các chương trình chung của Linux được sử dụng bởi tất cả người dùng được lưu ở đây. Ví dụ như: ps, ls, ping...

3. /sbin - Chương trình hệ thống
- Cũng giống như /bin, /sbinn cũng chứa các chương trình thực thi, nhưng chúng là những chương trình của admin, dành cho việc bảo trì hệ thống. Ví dụ như: reboot, fdisk, iptables...

4. /etc - Các file cấu hình
- Thư mục này chứa các file cấu hình của các chương trình, đồng thời nó còn chứa các shell script dùng để khởi động hoặc tắt các chương trình khác. Ví dụ: /etc/resolv.conf, /etc/logrolate.conf

5. /dev - Các file thiết bị
- Các phân vùng ổ cứng, thiết bị ngoại vi như USB, ổ đĩa cắm ngoài, hay bất cứ thiết bị nào gắn kèm vào hệ thống đều được lưu ở đây. Ví dụ: /dev/sdb1 là tên của USB bạn vừa cắm vào máy, để mở được USB này bạn cần sử dụng lệnh mount với quyền root: # mount /dev/sdb1 /tmp

6. /tmp - Các file tạm
- Thư mục này chứa các file tạm thời được tạo bởi hệ thống và các người dùng. Các file lưu trong thư mục này sẽ bị xóa khi hệ thống khởi động lại.

7. /proc - Thông tin về các tiến trình
- Thông tin về các tiến trình đang chạy sẽ được lưu trong /proc dưới dạng một hệ thống file thư mục mô phỏng. Ví dụ thư mục con /proc/{pid} chứa các thông tin về tiến trình có ID là pid (pid ~ process ID). Ngoài ra đây cũng là nơi lưu thông tin về về các tài nguyên đang sử dụng của hệ thống như: /proc/version, /proc/uptime...

8. /var - File về biến của chương trình
- Thông tin về các biến của hệ thống được lưu trong thư mục này. Như thông tin về log file: /var/log, các gói và cơ sở dữ liệu /var/lib...

9. /usr - Chương trình của người dùng
- Chứa các thư viện, file thực thi, tài liệu hướng dẫn và mã nguồn cho chương trình chạy ở level 2 của hệ thống. Trong đó
    + /usr/bin chứa các file thực thi của người dùng như: at, awk, cc, less... Nếu bạn không tìm thấy chúng trong /bin hãy tìm trong /usr/bin
    + /usr/sbin chứa các file thực thi của hệ thống dưới quyền của admin như: atd, cron, sshd... Nếu bạn không tìm thấy chúng trong /sbin thì hãy tìm trong thư mục này.
    + /usr/lib chứa các thư viện cho các chương trình trong /usr/bin và /usr/sbin
    + /usr/local chứa các chương tình của người dùng được cài từ mã nguồn. Ví dụ như bạn cài apache từ mã nguồn, nó sẽ được lưu dưới /usr/local/apache2
10. /home - Thư mục người của dùng
- Thư mục này chứa tất cả các file cá nhân của từng người dùng. Ví dụ: /home/john, /home/marie

11. /boot - Các file khởi động
- Tất cả các file yêu cầu khi khởi động như initrd, vmlinux. grub được lưu tại đây. Ví dụ vmlixuz-2.6.32-24-generic

12. /lib - Thư viện hệ thống
- Chứa cá thư viện hỗ trợ cho các file thực thi trong /bin và /sbin. Các thư viện này thường có tên bắt đầu bằng ld* hoặc lib*.so.*. Ví dụ như ld-2.11.1.so hay libncurses.so.5.7

13. /opt - Các ứng dụng phụ tùy chọn
- Tên thư mục này nghĩa là optional (tùy chọn), nó chứa các ứng dụng thêm vào từ các nhà cung cấp độc lập khác. Các ứng dụng này có thể được cài ở /opt hoặc một thư mục con của /opt

14. /mnt - Thư mục để mount
- Đây là thư mục tạm để mount các file hệ thống. Ví dụ như # mount /dev/sda2 /mnt

# Managing Files and Link: ‘ls’, ‘cp’, ‘mv’, ‘rm’, ‘touch’, ‘ln’

## ls (Liệt kê tệp và thư mục)

```bash
ls # - Hiển thị danh sách tệp/thư mục trong thư mục hiện tại.
ls -l #- Hiển thị danh sách chi tiết (dung lượng, quyền, chủ sở hữu, v.v.).
ls -a #- Hiển thị cả tệp ẩn.
ls -lh #- Hiển thị kích thước tệp ở định dạng dễ đọc (MB, GB).
```

## cp - Sao chép tệp/thư mục

```bash
cp file1 file2             # Sao chép file1 thành file2
cp -r dir1 dir2            # Sao chép thư mục dir1 vào dir2
cp -i file1 file2          # Hỏi xác nhận trước khi ghi đè
cp -u file1 file2          # Chỉ sao chép nếu file1 mới hơn file2

cp document.txt backup.txt  # Tạo bản sao của document.txt với tên backup.txt
cp -r /home/user/docs /backup/  # Sao chép thư mục docs vào /backup/
```

##  mv - Di chuyển hoặc đổi tên

```bash
mv file1 file2          # Đổi tên file1 thành file2
mv file1 /home/user/    # Di chuyển file1 vào thư mục khác
mv -i file1 file2       # Hỏi xác nhận trước khi ghi đè file2
mv dir1 dir2            # Đổi tên hoặc di chuyển thư mục

mv oldname.txt newname.txt   # Đổi tên file
mv myfile.txt /home/user/    # Di chuyển tệp vào thư mục khác
mv my_folder /backup/        # Di chuyển thư mục vào backup
```

## rm - Xóa tệp/thư mục

```bash
rm file1               # Xóa file1
rm -r dir1             # Xóa thư mục dir1 và nội dung bên trong
rm -i file1            # Hỏi xác nhận trước khi xóa
rm -rf dir1            # Xóa thư mục mà không cần xác nhận (nguy hiểm)

rm -i myfile.txt       # Hỏi xác nhận trước khi xóa
rm -r old_folder/      # Xóa toàn bộ thư mục old_folder
```

## Tạo hoặc cập nhật tệp

```bash
touch file1            # Tạo tệp trống file1
touch file1 file2      # Tạo nhiều tệp cùng lúc
touch -c file1         # Chỉ cập nhật thời gian truy cập nếu file tồn tại

touch newfile.txt      # Tạo file mới nếu chưa tồn tại
```

## Managing File Ownership: ‘chown’, ‘chgrp’

- Trong hệ thống mã nguồn mở linux file/folder luôn bị giới hạn bởi quyền và chủ sở hữu, đó là hai yếu tố không thể tách rời.
- Quyền (permission) trên linux:
    + Quyền đọc r được quy định bằng sô 4
    + Quyền ghi w được quy định bằng số 2
    + Quyền thực thi x được quy định bằng số 1
- Đối tượng sở hữu (owner) trên linux:
    + Đối tượng u hay còn gọi là user tạo ra file/folder. Bạn login bằng user nào thì file/folder tạo ra sẽ thuộc quản lý của user đó.
    + Đối tượng g được gọi là group dùng để nhóm và quản lý nhiều đối tượng  u. Vì trên linux đâu phải chỉ có mỗi user root duy nhất đâu.
    + Đối tượng o là các đối tượng khác không thuộc u, g

- Quyền 755 trên folder tương ứng với drwxr-xr-x có nghĩa là user sở hữu folder có full quyền, group sở hữu folder có quyền đọc và thực thi, other cũng có quyền đọc và thực thi.
- Quyền 644 trên file tương ứng với -rw-r--r-- có nghĩa là user sở hữu file này có quyền đọc và ghi, group sở hữu có quyền đọc, other cũng có quyền đọc mà thôi.


## chown

- Lệnh chown (change owner) cho phép bạn thay đổi chủ sở hữu (owner) và nhóm (group) của tệp/thư mục.

```bash
chown option user:group file/folder
# Khi thay chủ sở hữu cho folder ta có thể dùng thêm option -R để thay sở hữu cho cả file/folder con.
```

## chgrp
- Lệnh chgrp (change group) chỉ thay đổi nhóm sở hữu của tệp/thư mục mà không đổi chủ sở hữu.

```bash
chgrp [TÙY_CHỌN] GROUP TỆP
chgrp group1 file.txt  # Đổi nhóm sở hữu thành group1
chgrp -R group1 /home/user1  # Đổi nhóm của toàn bộ thư mục

```

## chmod

- Trên các hệ thống giống như Unix, chmod là một lệnh cấp hệ thống, viết tắt của “change mode” và cho phép bạn thay đổi cài đặt quyền của file theo cách thủ công.

```bash
chmod option permission file/folder
chmod option owner(+,-)(r,w,x) file/folder

chmod u+x file.txt  # Thêm quyền thực thi (execute) cho chủ sở hữu
chmod g-w file.txt  # Gỡ quyền ghi (write) cho nhóm
chmod o+r file.txt  # Cấp quyền đọc (read) cho người khác
chmod u=rwx,g=rx,o=r file.txt  # Thiết lập quyền cụ thể

chmod 755 file.txt  # rwxr-xr-x (Chủ có tất cả quyền, nhóm và người khác chỉ đọc + thực thi)
chmod 644 file.txt  # rw-r--r-- (Chủ có thể đọc + ghi, nhóm và người khác chỉ đọc)
chmod 777 file.txt  # rwxrwxrwx (Tất cả đều có quyền đọc, ghi, thực thi)
chmod 000 file.txt  # --- --- --- (Không ai có quyền gì)

chmod +x my_folder/  # Cho phép mở thư mục
chmod -R 755 my_folder/  # Thay đổi quyền của thư mục và tất cả tệp bên trong

```

# Tools for Locating Files: ‘find’, ‘locate’, ‘whereis’, ‘which’

- find: Tìm kiếm tệp và thư mục trong hệ thống tệp dựa trên các tiêu chí cụ thể như tên, loại, kích thước, ngày sửa đổi, v.v.

```bash
find [đường_dẫn_bắt_đầu] [type] [pattern]

#Tìm theo tên không phân biệt chữ hoa chữ thường:
find /path/to/search -iname "example.txt"
find /path/to/search -type f -iname "example.txt"
# Tìm tệp theo kích thước
find /path/to/search -size +500k # Tìm các tệp lớn hơn 500 kilobyte.
# Tìm các tệp được sửa đổi trong vòng 7 ngày qua.
find /path/to/search -mtime -7
# 
root@ubuntu-22-04:~# find . -type f  -name 'file*.txt'
./archive/file2.txt
./file1.txt
./file.txt
./file2.txt
root@ubuntu-22-04:~# find . -maxdepth 1 -type f -name "*.txt"
./file1.txt
./file.txt
./file2.txt
./users.txt
root@ubuntu-22-04:~# find . -type f -size 1k
./.profile
./archive/file1.txt.gz
./archive/file2.txt
# Filtering files by owner
find <directory> -user <user>
# Filtering files by group
find <directory> -group <group>
```

- locate: locate không quét trực tiếp hệ thống tệp theo thời gian thực. Thay vào đó, nó tìm kiếm trong một cơ sở dữ liệu (database) đã được tạo và cập nhật định kỳ bởi lệnh updatedb. Vì thế, kết quả trả về rất nhanh.
- Dữ liệu có thể lỗi thời: Nếu bạn vừa tạo, đổi tên hoặc xóa tệp, thông tin này sẽ không có trong cơ sở dữ liệu cho đến khi updatedb được chạy lại.

```bash
locate [OPTION] PATTERN...
locate .bashrc
locate *.md
locate -n 10 *.py
locate -n 10 *.py
locate -i readme.md
locate --count .bashrc
# Cập nhật cơ sở dữ liệu locate để đảm bảo kết quả tìm kiếm mới nhất.
sudo updatedb
```

- whereis: Xác định vị trí của các tệp nhị phân, mã nguồn và trang hướng dẫn (man page) liên quan đến một lệnh cụ thể.

```bash
whereis [OPTIONS] COMMAND...

# Chỉ hiển thị vị trí tệp thực thi của lệnh ls.
root@ubuntu-22-04:~# whereis -b ls
ls: /usr/bin/ls
# Chỉ hiển thị vị trí tệp nguồn của lệnh ls.
root@ubuntu-22-04:~# whereis -s ls
ls:
# Chỉ hiển thị vị trí trang hướng dẫn của lệnh ls.
root@ubuntu-22-04:~# whereis -m ls
ls: /usr/share/man/man1/ls.1.gz
```

- which: sử dụng để xác định vị trí đầy đủ của tệp thực thi (executable file) mà hệ thống sẽ gọi khi bạn nhập một lệnh vào terminal. Nghĩa là, khi bạn gõ một lệnh, which sẽ cho biết chương trình thực thi đó nằm ở đâu trong các thư mục được liệt kê trong biến môi trường PATH.

```bash
which [tùy_chọn] tên_lệnh

root@ubuntu-22-04:~# which ls
/usr/bin/ls
```

# The Boot Process

# Administering the System

## Nắm vững các lệnh tạo, sửa user/group cùng các option quan trọng. Phân biệt su, sudo, các file /etc/passwd , /etc/groups, /etc/sudoer

```bash
# Dùng để tạo một tài khoản người dùng mới.
adduser [new_account]
useradd [new_account]
sudo useradd -c "vanteo" -m -d /home/vanteo -s /bin/bash -g users -G sudo vanteo
# -c "Comment": Thêm mô tả hoặc thông tin cho user (ví dụ: tên thật).
# -d /home/username: Xác định thư mục home của user.
# -m: Tạo thư mục home nếu chưa có.
# -s /bin/bash: Chỉ định shell mặc định cho user.
# -g group: Chỉ định group chính cho user.
# -G group1,group2: Chỉ định các group phụ cho user.
# Đặt mật khẩu cho user:
sudo passwd vanteo

# thay đổi thông tin của user đã tồn tại.
usermod [options] [username]
sudo usermod -s /bin/zsh vanA
# -l newname: Đổi tên login của user.
# -d new_home -m: Đổi thư mục home và di chuyển nội dung cũ sang thư mục mới.
# -s /bin/zsh: Thay đổi shell.
sudo usermod -aG group1,group2,... username
# -G: Chỉ định danh sách các nhóm bổ sung mà người dùng sẽ thuộc về.
# -a: (append) Thêm các nhóm được chỉ định vào danh sách các nhóm hiện tại của người dùng. Nếu bỏ qua -a, lệnh sẽ thay thế danh sách nhóm hiện có bằng danh sách mới chỉ định.
# Xóa user, Option -r: Xóa luôn thư mục home và các file liên quan.
sudo userdel -r vanteo
```

```bash
# Tạo group
sudo groupadd developers
# Dùng để thay đổi thông tin của group, chẳng hạn đổi tên group.
sudo groupmod -n newgroup oldgroup
# Xóa group
sudo groupdel developers
```

### Phân biệt su và sudo

- su (Switch User): Dùng để chuyển đổi từ user hiện tại sang một user khác (thường là root). Khi dùng su mà không chỉ định user, mặc định chuyển sang root.

```bash
su -  # chuyển sang root
su username  # chuyển sang tài khoản khác
```

- sudo (Super User Do):
    + Cho phép chạy lệnh với quyền của user khác (mặc định là root) mà không cần chuyển hoàn toàn sang user đó.
    + Dựa vào file /etc/sudoers để xác định user nào có quyền sử dụng sudo và các lệnh được phép chạy.
    + Hạn chế quyền (chỉ cho phép chạy các lệnh đã cấu hình trong sudoers).

```bash
sudo apt update
```

- /etc/passwd: Lưu trữ thông tin tài khoản người dùng.

```bash
username:password:UID:GID:gecos:home_directory:shell
viettu:x:1000:1000:nguyen viet tu:/home/viettu:/bin/bash
# UID: Mã định danh user.
# GID: Mã định danh group chính.
# gecos: Thông tin mô tả (tên đầy đủ, số điện thoại, v.v.).
# Mật khẩu trong file này thường được thay thế bằng dấu "x" – mật khẩu thật được lưu trữ trong file /etc/shadow.

```

- /etc/group: Lưu trữ thông tin về các group trên hệ thống.

```bash
groupname:password:GID:user_list
# user_list: Danh sách các user thuộc group đó.
viettu:x:1000:vanteo
```

- /etc/sudoers (visudo): 
    + Xác định những người dùng và nhóm nào có quyền sử dụng sudo và quyền thực thi các lệnh nhất định với quyền cao.

```bash
# Cho phép user 'vanA' chạy mọi lệnh với sudo
vanA ALL=(ALL) ALL

# vanA: Tên người dùng được cấp quyền.
# ALL: Cho phép thực thi lệnh trên tất cả các máy (thường dùng cho máy đơn).
# (ALL): Cho phép thực thi lệnh với tất cả các người dùng và nhóm.
# ALL: Cho phép thực thi mọi lệnh.

# Cho phép nhóm 'sudo' chạy mọi lệnh
%sudo ALL=(ALL) ALL

# người dùng thực thi lệnh shutdown mà không cần nhập mật khẩu:
viettu ALL=(ALL) NOPASSWD: /sbin/shutdown
```

## Tìm hiểu các file log trong hệ thống, kỹ năng kiểm tra, đọc LOG để xem hệ thống có vấn đề gì hết sức quan trọng. Tìm hiểu cách để rotate LOG, tại sao phải thực hiện việc này.

- /var/log/syslog: Ghi lại các thông báo hệ thống chung, bao gồm cả thông tin từ kernel và các dịch vụ hệ thống.
- /var/log/auth.log: Lưu trữ các sự kiện liên quan đến xác thực, chẳng hạn như đăng nhập thành công hoặc thất bại.
- /var/log/kern.log: Chứa các thông báo từ kernel, hữu ích cho việc chẩn đoán các vấn đề liên quan đến kernel.
- /var/log/dpkg.log: Ghi lại thông tin về các gói được cài đặt, nâng cấp hoặc gỡ bỏ trên hệ thống Debian và Ubuntu.
- /var/log/boot.log: Lưu trữ các thông báo được tạo ra trong quá trình khởi động hệ thống.
- /var/log/httpd/ hoặc /var/log/apache2/: Chứa các log liên quan đến máy chủ web Apache, bao gồm access.log và error.log.
- /var/log/mysql/: Lưu trữ các log của máy chủ cơ sở dữ liệu MySQL.


```bash
# cat: Hiển thị toàn bộ nội dung của tệp log.
cat /var/log/syslog
# tail: Hiển thị phần cuối của tệp log, hữu ích để xem các sự kiện mới nhất.
tail /var/log/auth.log
# Sử dụng tùy chọn -f để theo dõi log theo thời gian thực:
tail -f /var/log/syslog
# less: Cho phép xem nội dung tệp log với khả năng cuộn và tìm kiếm.
less /var/log/kern.log
# Lọc thông tin quan trọng:
grep "error" /var/log/syslog  # Tìm lỗi
journalctl -p err -n 50  # Xem 50 dòng lỗi gần nhất trong systemd
journalctl -u nginx.service
```

## Rotate LOG

- Theo thời gian, các tệp log có thể trở nên rất lớn, chiếm dụng nhiều dung lượng đĩa và gây khó khăn trong việc quản lý. Để giải quyết vấn đề này, việc xoay vòng log (log rotation) được thực hiện.
- Log rotation là quá trình:
    + Đổi tên tệp log hiện tại (ví dụ: từ syslog thành syslog.1).
    + Tạo mới tệp log trống với tên ban đầu để hệ thống tiếp tục ghi log.
    + Nén các tệp log cũ để tiết kiệm dung lượng.
    + Xóa các tệp log quá cũ theo chính sách lưu trữ.

- Việc này giúp:
    + Tiết kiệm dung lượng đĩa: Tránh tình trạng tệp log phát triển không kiểm soát và chiếm dụng không gian lưu trữ.
    + Dễ dàng quản lý: Giúp việc xem xét và phân tích log trở nên hiệu quả hơn.
    + Bảo mật: Giới hạn thời gian lưu trữ của các thông tin nhạy cảm trong log.

- Trên hệ thống Linux, công cụ phổ biến để thực hiện log rotation là logrotate. Công cụ này cho phép tự động hóa quá trình xoay vòng, nén và xóa các tệp log dựa trên cấu hình định sẵn.

- Giả sử  muốn cấu hình Logrotate cho một tệp log cụ thể /var/log/example.log với các yêu cầu sau:
    + Xoay vòng tệp log hàng tuần.
    + Giữ lại 4 tệp log cũ.
    + Nén các tệp log cũ sau khi xoay vòng.
    + Không xoay vòng nếu tệp log trống.

```bash
$ vi /etc/logrotate.d/example
/var/log/example.log {
    weekly
    rotate 4
    compress
    notifempty
    missingok
    create 0640 root
}

# weekly: Xoay vòng tệp log hàng tuần.
# rotate 4: Giữ lại 4 tệp log cũ.
# compress: Nén các tệp log cũ sau khi xoay vòng.
# notifempty: Không xoay vòng nếu tệp log trống.
# missingok: Bỏ qua nếu tệp log không tồn tại.
# create 0640 root: Tạo tệp log mới với quyền truy cập 0640, thuộc sở hữu của người dùng root.
```

# Tìm hiểu cách thiết lập cron, khi nào có nhu cầu sử dụng cron

- Cron là một tiện ích trong hệ điều hành Unix và Linux, cho phép người dùng lập lịch để tự động thực hiện các tác vụ vào những thời điểm cụ thể hoặc theo chu kỳ định kỳ. Các tác vụ này, được gọi là "cron jobs", giúp tự động hóa các công việc lặp đi lặp lại, giảm thiểu sự can thiệp thủ công và đảm bảo tính nhất quán trong việc thực hiện.

- Khi nào nên sử dụng cron:
    + Sao lưu dữ liệu định kỳ: Tự động sao lưu cơ sở dữ liệu hoặc tệp quan trọng hàng ngày, hàng tuần hoặc hàng tháng để đảm bảo an toàn dữ liệu.
    + Bảo trì hệ thống: Thực hiện các tác vụ bảo trì như dọn dẹp tệp tạm thời, xóa các tệp nhật ký cũ hoặc kiểm tra hệ thống định kỳ.
    + Cập nhật dữ liệu: Tự động cập nhật hoặc đồng bộ hóa dữ liệu từ các nguồn bên ngoài vào hệ thống.
    + + Gửi thông báo: Gửi email hoặc thông báo vào những thời điểm cụ thể, chẳng hạn như báo cáo hàng ngày hoặc nhắc nhở.


```bash
# **crontab -e**: edits crontab entries to add, delete, or edit cron jobs.
# **crontab -l**: list all the cron jobs for the current user.
# **crontab -u username -l**: list another user's crons.
# **crontab -u username -e**: edit another user's crons.

*   *   *   *   *  sh /path/to/script/script.sh
|   |   |   |   |              |
|   |   |   |   |      Command or Script to Execute        
|   |   |   |   |
|   |   |   |   |
|   |   |   |   |
|   |   |   | Day of the Week(0-6)
|   |   |   |
|   |   | Month of the Year(1-12)
|   |   |
|   | Day of the Month(1-31)  
|   |
| Hour(0-23)  
|
Min(0-59)

# Thực hiện lệnh sao lưu tại 2:30 sáng hàng ngày
30 2 * * * /usr/local/bin/backup.sh
# Xóa các file tạm thời vào mỗi Chủ Nhật lúc 3:00 sáng:
0 3 * * 0 /usr/local/bin/cleanup.sh
# Gửi email báo cáo vào 8 giờ sáng thứ Hai đến thứ Sáu
0 8 * * 1-5 /usr/local/bin/send_report.sh
# Thay đổi tệp crontab của một người dùng khác: (chỉ dành cho root)
crontab -u username -e
```

# Tìm hiểu các cách để backup cơ bản hệ thống. Đặc biệt thành thạo lệnh rsync và chương trình rsnapshot.

Tại sao càn backup:
    + Phòng ngừa mất mát dữ liệu
    + Phục hồi sau thảm họa
    + Đảm bảo tính liên tục của hoạt động
    + Phục hồi hệ thống sau nâng cấp hoặc thay đổi cấu hình


## rsync
- Rsync (Remote Sync) là một công cụ dùng để sao chép và đồng bộ file/thư mục được dùng rất phổ biến. Với sự trợ giúp của rsync, bạn có thể đồng bộ dữ liệu trên local hoặc giữa các server với nhau một cách dễ dàng.
- rsync là một công cụ mạnh mẽ dùng để đồng bộ và sao lưu file, cho phép thực hiện sao lưu gia tăng (incremental backup) nhờ việc chỉ truyền các phần thay đổi của file.
- Tính năng nổi bật của Rsync: 
    + Rsync hỗ trợ copy giữ nguyên thông số của files/folder như Symbolic links, Permissions, TimeStamp, Owner và Group.
    + Rsync nhanh hơn scp vì Rsync sử dụng giao thức remote-update, chỉ transfer những dữ liệu thay đổi mà thôi.
    + Rsync tiết kiệm băng thông do sử dụng phương pháp nén và giải nén khi transfer.
    + Rsync không yêu cầu quyền super-user.


```bash
rsync options source destination

# Source: dữ liệu nguồn
# Destination: dữ liệu đích
# Options: một số tùy chọn thêm

# Các tham số cần biết khi dùng Rsync:
    # -v: hiển thị trạng thái kết quả
    # -r: copy dữ liệu recursively, nhưng không đảm bảo thông số của file và thư mục
    # -a: cho phép copy dữ liệu recursively, đồng thời giữ nguyên được tất cả các thông số của thư mục và file
    # -z: nén dữ liệu khi transfer, tiết kiệm băng thông tuy nhiên tốn thêm một chút thời gian
    # -h: human-readable, output kết quả dễ đọc
    # --delete: xóa dữ liệu ở destination nếu source không tồn tại dữ liệu đó.
    # --exclude: loại trừ ra những dữ liệu không muốn truyền đi, nếu bạn cần loại ra nhiều file hoặc folder ở nhiều đường dẫn khác nhau thì mỗi cái bạn phải thêm --exclude tương ứng.
# Rsync không tự động chạy nên thường được dùng kết hợp với crontab.
```

```bash
root@ubuntu-22-04:~# touch backup.tar
root@ubuntu-22-04:~# mkdir /tmp/backup
root@ubuntu-22-04:~# rsync -zvh backup.tar /tmp/backups/
created directory /tmp/backups
backup.tar

sent 82 bytes  received 70 bytes  304.00 bytes/sec
total size is 0  speedup is 0.00

# Câu lệnh trên copy toàn bộ file từ thư mục /root/rpmpkgs đến thư mục /tmp/backups/ trên cùng một máy.
rsync -avzh /root/rpmpkgs /tmp/backups/

# Copy thư mục từ Local lên Remote Server
# copy thư mục rpmpkgs từ Local lên Remote Server có IP 192.168.0.101, lưu ở thư mục /home/
rsync -avz rpmpkgs/ root@192.168.0.101:/home/

# Rsync qua SSH
# Copy file từ Remote Server về Local Server qua SSH
rsync -avzhe ssh root@192.168.0.100:/root/install.log /tmp

# Nếu muốn xóa một file hoặc thư mục không có ở thư mục nguồn, mà lại xuất hiện ở thư mục đích trong quá trình transfer, bạn hãy sử dụng tùy chọn --delete.

$ touch test.txt
$ rsync -avz --delete root@192.168.0.100:/var/lib/rpm/ .
Password:
receiving file list ... done
deleting test.txt
./
sent 26 bytes  received 390 bytes  48.94 bytes/sec
total size is 45305958  speedup is 108908.55

# Giới hạn dung lượng tối đa của file được đồng bộ
rsync -avzhe ssh --max-size='200k' /var/lib/rpm/ root@192.168.0.100:/root/tmprpm
# Tự động xóa dữ liệu nguồn sau khi đồng bộ thành công
rsync --remove-source-files -zvh backup.tar /tmp/backups/
# Giới hạn bandwidth
rsync --bwlimit=100 -avzhe ssh  /var/lib/rpm/  root@192.168.0.100:/root/tmprpm/
```

## rsnapshot là một chương trình backup tự động dựa trên rsync và hard links, giúp thực hiện sao lưu gia tăng một cách hiệu quả.

- Ưu điểm:
    + Backup gia tăng (incremental backups): Chỉ lưu các thay đổi, nhờ đó tiết kiệm dung lượng lưu trữ.
    + Tự động hóa: Quá trình backup được lập lịch (thường thông qua cron) và không cần can thiệp thủ công sau khi cấu hình.
    + Khôi phục dễ dàng: Duy trì các bản backup theo lịch trình (hàng giờ, hàng ngày, hàng tuần…).
- Cấu hình cơ bản:
    + File cấu hình thường nằm tại /etc/rsnapshot.conf.
    + Bạn cần thiết lập các tham số như thư mục sao lưu, thời gian giữ bản backup, số lượng bản lưu trữ…

```/etc/rsnapshot.conf
snapshot_root   /backup/rsnapshot/
no_create_root  1

# Thiết lập tần suất backup
interval        hourly  6
interval        daily   7
interval        weekly  4
interval        monthly 3

# Định nghĩa các nguồn cần backup
backup  /home/   home
backup  /etc/    etc

<!-- Thư mục /home/ vào /backup/home/
Thư mục /etc/ vào /backup/etc/ -->
```

```bash
# Lập lịch chạy bằng cron: Thêm các dòng sau vào crontab (sử dụng crontab -e) để chạy rsnapshot theo lịch:
# Backup hàng giờ
0 * * * * /usr/bin/rsnapshot hourly
# Backup hàng ngày lúc 3:00 sáng
0 3 * * * /usr/bin/rsnapshot daily
# Backup hàng tuần vào Chủ nhật lúc 4:00 sáng
0 4 * * 0 /usr/bin/rsnapshot weekly
```