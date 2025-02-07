# OSI ?
- Mô hình OSI (Open Systems Interconnection)
- Mô hình OSI (Open Systems Interconnection) là một mô hình tham chiếu để mô tả cách mà các hệ thống mạng hoạt động (máy tính sử dụng để giao tiếp qua mạng)
# Nắm được vai trò từng layer trong các mô hình.

## Tầng 7 – Application Layer ( Tầng ứng dụng)
- Tầng ứng dụng là lớp trên cùng, xác định giao diện giữa người sử dụng và môi trường OSI. Tầng ứng dụng được sử dụng bởi phần mềm người dùng cuối như trình duyệt web và ứng dụng email. Nó cung cấp các giao thức cho phép phần mềm gửi, nhận thông tin và trình bày dữ liệu có ý nghĩa cho người dùng.

====
- Đây là tầng duy nhất tương tác trực tiếp với dữ liệu từ người dùng. Các phần mềm ứng dụng như web browser và email client dựa vào Application layer để khởi tạo giao tiếp. Tuy nhiên, cần làm rõ rằng client software applications không phải là một phần của Application layer; thay vào đó, Application layer chịu trách nhiệm về các protocol và xử lý dữ liệu mà phần mềm sử dụng để hiển thị thông tin có ý nghĩa cho người dùng.

- Các Application layer protocols bao gồm HTTP cũng như SMTP (Simple Mail Transfer Protocol - một trong những protocols cho phép email communications).

====
- Đây là tầng trên cùng của mô hình OSI, đảm bảo kết nối giữa người dùng và hệ thống. Tầng này được sử dụng bởi các phần mềm như trình duyệt web hay ứng dụng email, cung cấp các giao thức hỗ trợ gửi, nhận và trình bày dữ liệu một cách có ý nghĩa cho người dùng cuối.

## Tầng 6 – Presentation Layer (Tầng Trình Bày)
- Tầng này chủ yếu chịu trách nhiệm chuẩn bị dữ liệu để có thể được sử dụng bởi Application layer; nói cách khác, Layer 6 giúp dữ liệu có thể được các ứng dụng tiếp nhận. Presentation layer chịu trách nhiệm về translation, encryption và compression của dữ liệu.

===
- Tầng trình bày xác định cách hai thiết bị sẽ mã hóa và nén dữ liệu để nó được nhận một cách chính xác ở đầu bên kia. Tầng trình bày lấy bất kỳ dữ liệu nào được truyền bởi tầng ứng dụng và chuẩn bị cho việc truyền qua tầng phiên.

- Tầng này chịu trách nhiệm chính trong việc chuẩn bị dữ liệu để nó có thể được sử dụng bởi tầng ứng dụng. Nói cách khác, tầng 6 làm cho dữ liệu hiển thị cho các ứng dụng sử dụng. Tầng trình bày chịu trách nhiệm dịch, mã hóa và nén dữ liệu.

- Hai thiết bị đang giao tiếp có thể sử dụng các phương pháp mã hóa khác nhau, do đó tầng 6 chịu trách nhiệm dịch dữ liệu đến thành một cú pháp mà lớp ứng dụng của thiết bị nhận có thể hiểu được. Nếu các thiết bị đang giao tiếp qua kết nối được mã hóa, tầng 6 chịu trách nhiệm thêm mã hóa ở đầu người gửi cũng như giải mã mã hóa ở đầu người nhận để nó có thể hiển thị tầng ứng dụng với dữ liệu có thể đọc được, không được mã hóa.

- Cuối cùng, lớp trình bày cũng chịu trách nhiệm nén dữ liệu mà nó nhận được từ lớp ứng dụng trước khi phân phối đến tầng 5. Điều này giúp cải thiện tốc độ và hiệu quả của giao tiếp bằng cách giảm thiểu lượng dữ liệu sẽ được truyền.

## Tầng 5 – Session Layer (Tầng phiên)
- Đây là lớp chịu trách nhiệm đóng mở giao tiếp giữa hai thiết bị. Khoảng thời gian từ khi giao tiếp được mở và đóng được gọi là phiên. Tầng phiên đảm bảo rằng phiên vẫn mở đủ lâu để chuyển tất cả dữ liệu đang được trao đổi, và sau đó nhanh chóng đóng phiên để tránh lãng phí tài nguyên.
- Lớp phiên cũng đồng bộ hóa việc truyền dữ liệu với các điểm kiểm tra.

- Ví dụ: Nếu một tệp 100 megabyte đang được chuyển, tầng phiên có thể đặt một điểm kiểm tra cứ sau 5 megabyte. Trong trường hợp ngắt kết nối hoặc gặp sự cố sau khi 52 megabyte đã được chuyển, phiên có thể được tiếp tục từ điểm kiểm tra cuối cùng, có nghĩa là chỉ cần chuyển thêm 50 megabyte dữ liệu. Nếu không có các trạm kiểm soát, toàn bộ quá trình chuyển sẽ phải bắt đầu lại từ đầu.

===

- Session layer cũng đồng bộ hóa quá trình truyền dữ liệu bằng các checkpoint. Ví dụ, nếu một tệp 100 megabyte đang được truyền, Session layer có thể đặt một checkpoint sau mỗi 5 megabyte. Trong trường hợp mất kết nối hoặc sự cố xảy ra sau khi đã truyền được 52 megabyte, phiên truyền dữ liệu có thể được tiếp tục từ checkpoint cuối cùng, nghĩa là chỉ cần truyền thêm 50 megabyte nữa. Nếu không có checkpoint, toàn bộ quá trình truyền sẽ phải bắt đầu lại từ đầu.

## Tầng 4 – Transport Layer (Tầng Vận Chuyển)

- Tầng 4 chịu trách nhiệm giao tiếp đầu cuối giữa hai thiết bị. Điều này bao gồm việc lấy dữ liệu từ lớp phiên và chia nó thành các phần được gọi là phân đoạn trước khi gửi đến tầng 3. Tầng truyền tải trên thiết bị nhận có trách nhiệm tập hợp lại các phân đoạn thành dữ liệu mà tầng phiên có thể sử dụng.

- Tầng vận chuyển cũng chịu trách nhiệm kiểm soát luồng và kiểm soát lỗi. Kiểm soát luồng xác định tốc độ truyền tối ưu để đảm bảo rằng người gửi có kết nối nhanh không làm người nhận có kết nối chậm bị choáng ngợp. Tầng truyền tải thực hiện kiểm soát lỗi ở đầu nhận bằng cách đảm bảo rằng dữ liệu nhận được là hoàn chỉnh và yêu cầu truyền lại nếu chưa.


====

- Layer 4 chịu trách nhiệm về giao tiếp end-to-end giữa hai thiết bị. Điều này bao gồm việc lấy dữ liệu từ Session layer và chia nhỏ thành các phần gọi là segment trước khi gửi xuống Layer 3. Transport layer trên thiết bị nhận sẽ chịu trách nhiệm tập hợp lại các segment thành dữ liệu mà Session layer có thể sử dụng.

- Transport layer cũng đảm nhiệm flow control và error control. Flow control xác định tốc độ truyền tối ưu để đảm bảo rằng một sender có kết nối nhanh không làm quá tải một receiver có kết nối chậm. Transport layer thực hiện error control ở phía nhận bằng cách đảm bảo dữ liệu nhận được là đầy đủ và yêu cầu truyền lại nếu dữ liệu bị thiếu.

- Các Transport layer protocols bao gồm Transmission Control Protocol (TCP) và User Datagram Protocol (UDP).

## Tầng 3 – Network Layer (Tầng Mạng)

- Tầng mạng có nhiệm vụ tạo điều kiện thuận lợi cho việc truyền dữ liệu giữa hai mạng khác nhau. Nếu hai thiết bị giao tiếp trên cùng một mạng, thì tầng mạng là không cần thiết. Tầng mạng chia nhỏ các phân đoạn từ lớp truyền tải thành các đơn vị nhỏ hơn, được gọi là gói, trên thiết bị của người gửi và tập hợp lại các gói này trên thiết bị nhận. Tầng mạng cũng tìm ra con đường vật lý tốt nhất để dữ liệu đến đích của nó; điều này được gọi là định tuyến.

====
- Network layer chịu trách nhiệm hỗ trợ việc truyền dữ liệu giữa hai mạng khác nhau. Nếu hai thiết bị giao tiếp nằm trong cùng một mạng, thì Network layer không cần thiết. Network layer chia nhỏ các segment từ Transport layer thành các đơn vị nhỏ hơn, gọi là packet, trên thiết bị gửi và tập hợp lại các packet này trên thiết bị nhận.

Network layer cũng tìm đường đi vật lý tối ưu nhất để dữ liệu đến đích, quá trình này được gọi là routing.

Các Network layer protocols bao gồm IP, Internet Control Message Protocol (ICMP), Internet Group Message Protocol (IGMP) và bộ giao thức IPsec.

## Tầng 2 – Data Link Layer (Tầng liên kết)

- Tầng liên kết dữ liệu rất giống với tầng mạng, ngoại trừ tầng liên kết dữ liệu tạo điều kiện thuận lợi cho việc truyền dữ liệu giữa hai thiết bị trên cùng một mạng . Tầng liên kết dữ liệu lấy các gói từ tầng mạng và chia chúng thành các phần nhỏ hơn gọi là khung. Giống như tầng mạng, tầng liên kết dữ liệu cũng chịu trách nhiệm điều khiển luồng và điều khiển lỗi trong giao tiếp nội mạng (Tầng vận chuyển chỉ làm nhiệm vụ điều khiển luồng và điều khiển lỗi cho truyền thông giữa các mạng).


===

- Data link layer rất giống với Network layer, nhưng điểm khác biệt là Data link layer hỗ trợ truyền dữ liệu giữa hai thiết bị trong cùng một mạng. Data link layer nhận các packet từ Network layer và chia nhỏ chúng thành các phần nhỏ hơn gọi là frame.

- Giống như Network layer, Data link layer cũng chịu trách nhiệm về flow control và error control trong giao tiếp nội mạng (intra-network). Trong khi đó, Transport layer chỉ thực hiện flow control và error control cho giao tiếp liên mạng (inter-network).


===
Nội mạng (Intra-network): Là mạng nội bộ, tức là các thiết bị giao tiếp với nhau trong cùng một mạng, không cần đi qua bộ định tuyến (router). Ví dụ, các máy tính trong cùng một mạng LAN (Local Area Network) có thể truyền dữ liệu trực tiếp với nhau thông qua switch hoặc hub mà không cần kết nối ra bên ngoài.

Liên mạng (Inter-network): Là giao tiếp giữa các thiết bị thuộc các mạng khác nhau, cần sử dụng router để định tuyến dữ liệu. Ví dụ, khi một máy tính trong mạng nội bộ gửi dữ liệu đến một máy chủ trên Internet, dữ liệu phải đi qua nhiều mạng trung gian, bao gồm cả mạng của nhà cung cấp dịch vụ Internet (ISP).

Tóm lại:

Nội mạng: Giao tiếp trong cùng một mạng cục bộ (LAN).
Liên mạng: Giao tiếp giữa các mạng khác nhau (WAN, Internet).

## Tầng 1 – Physical Layer (Tầng Vật Lý)

- Lớp này bao gồm các thiết bị vật lý liên quan đến việc truyền dữ liệu, chẳng hạn như cáp và thiết bị chuyển mạch. Đây cũng là lớp nơi dữ liệu được chuyển đổi thành một luồng bit, là một chuỗi gồm các số 1 và 0. Lớp vật lý của cả hai thiết bị cũng phải đồng ý về một quy ước tín hiệu để các số 1 có thể được phân biệt với các số 0 trên cả hai thiết bị.

## Cách dữ liệu di chuyển qua mô hình OSI
Để thông tin có thể đọc được bởi con người được truyền qua mạng từ một thiết bị này đến một thiết bị khác, dữ liệu phải đi xuống qua bảy tầng của mô hình OSI trên thiết bị gửi và sau đó đi lên qua bảy tầng trên thiết bị nhận.

Ví dụ: Ông Cooper muốn gửi email cho bà Palmer. Ông Cooper soạn tin nhắn trong ứng dụng email trên laptop của mình, sau đó nhấn "gửi". Ứng dụng email sẽ chuyển email của ông đến Application layer, tầng này sẽ chọn một giao thức (SMTP) và chuyển dữ liệu xuống Presentation layer. Presentation layer sẽ nén dữ liệu, sau đó dữ liệu sẽ được chuyển đến Session layer, nơi khởi tạo phiên giao tiếp.

Tiếp theo, dữ liệu sẽ đến Transport layer, nơi nó sẽ được chia thành các segment. Những segment này sẽ được chia nhỏ hơn thành packet tại Network layer, sau đó được chia thành frame tại Data Link layer. Data Link layer sẽ chuyển frame đến Physical layer, nơi dữ liệu được chuyển đổi thành một dòng bit gồm các số 1 và 0 và gửi đi qua một phương tiện truyền dẫn vật lý, chẳng hạn như cáp mạng.

Khi máy tính của bà Palmer nhận được bit stream thông qua một phương tiện vật lý (chẳng hạn như Wi-Fi), dữ liệu sẽ chảy qua các tầng của mô hình OSI theo thứ tự ngược lại. Đầu tiên, Physical layer sẽ chuyển đổi bit stream từ các số 1 và 0 thành frame và gửi chúng đến Data Link layer. Data Link layer sẽ tập hợp lại các frame thành packet để gửi đến Network layer. Network layer sẽ tạo ra các segment từ packet cho Transport layer, nơi các segment sẽ được tập hợp lại thành một khối dữ liệu hoàn chỉnh.

Sau đó, dữ liệu sẽ tiếp tục đến Session layer, nơi nó được chuyển đến Presentation layer và phiên giao tiếp sẽ kết thúc. Presentation layer sẽ giải nén dữ liệu và chuyển dữ liệu thô lên Application layer. Application layer sau đó sẽ cung cấp dữ liệu ở dạng con người có thể đọc được cho phần mềm email của bà Palmer, giúp bà có thể đọc email của ông Cooper trên màn hình laptop của mình.

# Mô hình TCP/IP là gì?

- Mô hình TCP/IP tiêu chuẩn được chia thành 4 tầng (Layer) chồng lên nhau bao gồm: Tầng vật lý (Physical) → Tầng mạng (Network) → Tầng giao vận (Transport) và cuối cùng là Tầng ứng dụng (Application). Mỗi tầng đều có giao thức cụ thể khác nhau.
+ Tầng 4 – Tầng Ứng Dụng (Application)
+ Tầng 3 – Tầng Giao Vận (Transport)
+ Tầng 2 – Tầng Mạng (Internet)
+ Tầng 1 – Tầng Vật Lý (Physical)