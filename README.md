## Giao thức GRE

1. Gioi thieu ve giao thuc GRE :

- GRE – Generic Routing Encapsulation là giao thức được phát triển đầu tiên bởi Cisco, với mục đích chính tạo ra kênh truyền ảo (tunnel) để mang các giao thức lớp 3 thông qua mạng IP.

- Gói tin sau khi đóng gói được truyền qua mạng IP và sử dụng GRE header để định tuyến.

- Mỗi lần gói tin GRE đi đến đích, lần lượt các header ngoài cùng sẽ được gỡ bỏ cho đến khi gói tin ban đầu được mở ra.

2. Cơ chế hoạt động của GRE:

 - Để tạo ra các kênh truyền, GRE cũng thực hiện việc đóng gói gói tin tương tự như giao thức IPSec hoạt động ở Tunnel mode.
 - Trong quá trình đóng gói, GRE sẽ thêm các header mới vào gói tin, và header mới này cung cấp các thông số cần thiết dùng để truyền các gói tin thông qua môi trường trung gian.

- GRE chèn ít nhất 24 byte vào đầu gói tin:

  - Trong đó 20 byte là IP header mới dùng để định tuyến.
  
  - Còn 4 byte là GRE header.
  
  - Ngoài ra GRE còn có thể tùy chọn thêm 12 byte mở rộng để cung cấp tính năng tin cậy như: checksum, key chứng thực, sequence number.

 Cấu trúc một gói tin ip có thêm GRE header

 <img src="http://i.imgur.com/tsQfCyN.jpg">


Hai byte đầu tiên trong phần GRE header là GRE flag 2-byte. Phần bày chứa các cờ dùng để chỉ định những tính năng tùy chọn của GRE : 

– Bit 0 (checksum): Nếu bit này được bật lên (giá trị bằng 1) thỉ phần checksum được thêm vào sau trường Protocol type của GRE header.

– Bit 2 (key): Nếu bit này được bật lên thì phần Key tính năng chứng thực sẽ được áp dụng, đây như dạng password ở dạng clear text. Khi một thiết bị tạo nhiều tunnel đến nhiều thiết bị đích thì key này được dùng để xác định các thiết bị đích.

– Bit 3 (Sequence number): Khi bit này được bật thì phần sequence number được thêm vào GRE header.

Hai byte tiếp theo là phần Protocol Type chỉ ra giao thức lớp 3 của gói tin ban đầu được GRE đóng gói và truyền qua kênh truyền.

Khi gói tin đi qua tunnel nó sẽ được thêm GRE header như trên và sau khi tới đầu bên kia của kênh truyền, gói tin sẽ được loại bỏ GRE header để trả lại gói tin ban đầu.


3. GRE trong Openvswitch :










 

