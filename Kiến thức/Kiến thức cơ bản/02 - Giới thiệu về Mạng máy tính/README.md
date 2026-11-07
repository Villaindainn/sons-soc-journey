# 02 - Kiến thức cơ bản về Mạng máy tính (Computer Networking Basics)

## 1. Tổng quan

Bài học này giới thiệu những kiến thức nền tảng về mạng máy tính và cách các thiết bị giao tiếp, trao đổi dữ liệu với nhau.

### Mục tiêu bài học

- Hiểu mục đích và các loại mạng máy tính.
- Nắm được mô hình OSI và TCP/IP.
- Hiểu địa chỉ IP, địa chỉ MAC và Port.
- Phân biệt giao thức TCP và UDP.
- Hiểu DNS, DHCP và ARP.
- Giải thích được quá trình dữ liệu truyền đi khi truy cập một website.

---

## 2. Mạng máy tính là gì?

**Mạng máy tính (Computer Network)** là tập hợp các thiết bị được kết nối với nhau để giao tiếp, trao đổi dữ liệu và chia sẻ tài nguyên.

### 2.1. Các loại mạng phổ biến

| Loại mạng | Tên đầy đủ | Mô tả | Ví dụ |
|---|---|---|---|
| PAN | Personal Area Network | Mạng cá nhân trong phạm vi nhỏ | Thiết bị Bluetooth |
| LAN | Local Area Network | Mạng trong phạm vi giới hạn | Mạng gia đình, trường học |
| WLAN | Wireless Local Area Network | Mạng LAN sử dụng kết nối không dây | Mạng Wi-Fi |
| MAN | Metropolitan Area Network | Mạng trong phạm vi thành phố hoặc khu vực lớn | Mạng kết nối các cơ sở trong thành phố |
| WAN | Wide Area Network | Mạng trên phạm vi địa lý rộng | Internet |

### 2.2. Các thiết bị mạng phổ biến

| Thiết bị | Chức năng |
|---|---|
| Router | Chuyển tiếp các gói tin giữa những mạng khác nhau |
| Switch | Kết nối các thiết bị trong mạng LAN và chuyển tiếp frame dựa trên địa chỉ MAC |
| Access Point | Cho phép thiết bị không dây kết nối vào mạng |
| Modem | Kết nối mạng nội bộ với nhà cung cấp dịch vụ Internet |
| Firewall | Cho phép hoặc chặn lưu lượng mạng dựa trên các quy tắc bảo mật |

---

## 3. Mô hình OSI

**OSI Model (Open Systems Interconnection Model)** là mô hình tham chiếu mô tả quá trình giao tiếp mạng thông qua 7 tầng.

### 3.1. Bảy tầng trong mô hình OSI

| Tầng | Tên tầng | Chức năng chính | Ví dụ |
|---|---|---|---|
| 7 | Application – Ứng dụng | Cung cấp dịch vụ mạng cho ứng dụng | HTTP, DNS, SMTP |
| 6 | Presentation – Trình diễn | Xử lý biểu diễn, mã hóa và định dạng dữ liệu | Định dạng dữ liệu, mã hóa |
| 5 | Session – Phiên | Thiết lập và quản lý phiên giao tiếp | Quản lý phiên |
| 4 | Transport – Giao vận | Giao tiếp giữa các tiến trình | TCP, UDP |
| 3 | Network – Mạng | Định địa chỉ logic và định tuyến | IP, Router |
| 2 | Data Link – Liên kết dữ liệu | Truyền frame trong mạng cục bộ | Ethernet, MAC |
| 1 | Physical – Vật lý | Truyền các bit qua môi trường vật lý | Cáp mạng, tín hiệu vô tuyến |

### 3.2. Quá trình đóng gói dữ liệu (Data Encapsulation)

Khi dữ liệu được gửi qua mạng, mỗi tầng bổ sung thông tin cần thiết để phục vụ quá trình truyền nhận.

Quá trình đóng gói cơ bản:

`Application Data → Segment/Datagram → Packet → Frame → Bits`

Ý nghĩa:

- **Data:** Dữ liệu từ ứng dụng.
- **Segment/Datagram:** Dữ liệu ở tầng giao vận.
- **Packet:** Gói tin ở tầng mạng.
- **Frame:** Khung dữ liệu ở tầng liên kết dữ liệu.
- **Bits:** Các bit được truyền qua môi trường vật lý.

Ở thiết bị nhận, quá trình diễn ra ngược lại, được gọi là **Decapsulation (Tháo gói dữ liệu)**.

---

## 4. Mô hình TCP/IP

**TCP/IP Model** là mô hình mô tả các giao thức được sử dụng trong quá trình giao tiếp thực tế trên Internet.

### 4.1. Các tầng của mô hình TCP/IP

| Tầng | Chức năng chính | Ví dụ |
|---|---|---|
| Application – Ứng dụng | Cung cấp dịch vụ mạng cho ứng dụng | HTTP, HTTPS, DNS, DHCP |
| Transport – Giao vận | Xử lý giao tiếp giữa các tiến trình | TCP, UDP |
| Internet – Liên mạng | Định địa chỉ logic và định tuyến | IPv4, IPv6, ICMP |
| Network Access – Truy cập mạng | Xử lý giao tiếp trong mạng cục bộ | Ethernet, Wi-Fi, MAC |

### 4.2. So sánh mô hình OSI và TCP/IP

| Mô hình OSI | Mô hình TCP/IP |
|---|---|
| Application – Tầng 7 | Application |
| Presentation – Tầng 6 | Application |
| Session – Tầng 5 | Application |
| Transport – Tầng 4 | Transport |
| Network – Tầng 3 | Internet |
| Data Link – Tầng 2 | Network Access |
| Physical – Tầng 1 | Network Access |

**Ghi nhớ:** Mô hình OSI có 7 tầng, trong khi mô hình TCP/IP thường được trình bày với 4 tầng.

---

## 5. Địa chỉ IP

**IP Address (Internet Protocol Address)** là địa chỉ logic được sử dụng để xác định giao diện mạng và hỗ trợ định tuyến các gói tin giữa các mạng.

### 5.1. IPv4

IPv4 sử dụng địa chỉ có độ dài 32 bit, thường được biểu diễn bằng 4 nhóm số thập phân ngăn cách bởi dấu chấm.

Ví dụ:

`192.168.1.10`

Mỗi nhóm có giá trị từ 0 đến 255.

### 5.2. IPv6

IPv6 sử dụng địa chỉ có độ dài 128 bit, cung cấp không gian địa chỉ lớn hơn rất nhiều so với IPv4.

Ví dụ:

`2001:db8::1`

### 5.3. Địa chỉ IP Public và Private

| Loại | Mô tả | Ví dụ |
|---|---|---|
| Public IP | Địa chỉ sử dụng để giao tiếp trên Internet công cộng | Địa chỉ được ISP cấp |
| Private IP | Địa chỉ sử dụng trong mạng riêng | `192.168.1.10` |

Các dải địa chỉ IPv4 riêng phổ biến:

- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

### 5.4. Subnet Mask và CIDR

**Subnet Mask** là mặt nạ mạng dùng để xác định phần địa chỉ IP thuộc về mạng và phần thuộc về thiết bị (host).

Ví dụ:

- IP Address: `192.168.1.10`
- Subnet Mask: `255.255.255.0`
- CIDR: `/24`
- Network Address: `192.168.1.0`

**Giải thích:**

`/24` có nghĩa là 24 bit đầu tiên được sử dụng để xác định địa chỉ mạng, 8 bit còn lại được sử dụng để xác định địa chỉ host.

---

## 6. Địa chỉ MAC và giao thức ARP

### 6.1. MAC Address

**MAC Address (Media Access Control Address)** là địa chỉ ở tầng liên kết dữ liệu, được sử dụng để nhận diện giao diện mạng trong phạm vi mạng cục bộ.

Ví dụ:

`00:1A:2B:3C:4D:5E`

MAC Address thường được biểu diễn bằng 6 nhóm số hệ thập lục phân.

### 6.2. ARP – Address Resolution Protocol

ARP là giao thức được sử dụng trong mạng IPv4 để tìm địa chỉ MAC tương ứng với một địa chỉ IPv4 đã biết.

### 6.3. Quá trình hoạt động cơ bản của ARP

1. Một thiết bị cần gửi dữ liệu đến thiết bị khác trong cùng mạng cục bộ.
2. Thiết bị kiểm tra ARP Cache để tìm địa chỉ MAC tương ứng.
3. Nếu chưa có thông tin, thiết bị gửi một ARP Request dưới dạng Broadcast.
4. Thiết bị có địa chỉ IP phù hợp gửi lại ARP Reply.
5. Thiết bị gửi lưu kết quả vào ARP Cache để sử dụng sau này.

**Lưu ý:** IPv6 sử dụng giao thức Neighbor Discovery Protocol (NDP) thay thế cho ARP.

---

## 7. TCP và UDP

TCP và UDP là hai giao thức thuộc tầng Transport (Giao vận).

### 7.1. So sánh TCP và UDP

| Đặc điểm | TCP | UDP |
|---|---|---|
| Kết nối | Hướng kết nối (Connection-oriented) | Không hướng kết nối (Connectionless) |
| Độ tin cậy | Truyền dữ liệu tin cậy, có thứ tự | Không đảm bảo dữ liệu đến nơi hoặc đúng thứ tự |
| Khôi phục lỗi | Có cơ chế truyền lại dữ liệu bị mất | Không có cơ chế truyền lại tích hợp |
| Chi phí xử lý | Cao hơn | Thấp hơn |
| Ứng dụng phổ biến | HTTPS, SSH, Email | DNS, VoIP, Streaming, Game Online |

### 7.2. TCP Three-Way Handshake

TCP thiết lập kết nối thông qua quá trình bắt tay ba bước:

1. **SYN:** Client gửi yêu cầu thiết lập kết nối đến Server.
2. **SYN-ACK:** Server xác nhận yêu cầu và phản hồi lại Client.
3. **ACK:** Client xác nhận phản hồi của Server.

Sau khi hoàn thành ba bước, hai thiết bị có thể bắt đầu trao đổi dữ liệu.

### 7.3. Các cờ TCP phổ biến

| Flag | Ý nghĩa |
|---|---|
| SYN | Khởi tạo kết nối |
| ACK | Xác nhận dữ liệu hoặc thông điệp kết nối |
| FIN | Đóng kết nối một cách bình thường |
| RST | Đặt lại hoặc kết thúc kết nối ngay lập tức |
| PSH | Yêu cầu chuyển dữ liệu đến ứng dụng nhận |

---

## 8. Port và các giao thức phổ biến

**Port (Cổng mạng)** là số định danh điểm cuối của một dịch vụ hoặc ứng dụng trên thiết bị.

### Các cổng mạng thường gặp

| Port | Giao thức | Chức năng |
|---|---|---|
| 20/21 | TCP | FTP – Truyền tệp |
| 22 | TCP | SSH – Truy cập từ xa an toàn |
| 25 | TCP | SMTP – Gửi email |
| 53 | TCP/UDP | DNS – Phân giải tên miền |
| 67/68 | UDP | DHCP – Cấp phát địa chỉ IP |
| 80 | TCP | HTTP – Truyền tải nội dung web |
| 443 | TCP | HTTPS – Truyền tải web có mã hóa TLS |
| 123 | UDP | NTP – Đồng bộ thời gian |

**Lưu ý quan trọng:** Chỉ dựa vào số Port không thể khẳng định chắc chắn ứng dụng đang chạy là gì. Các dịch vụ có thể sử dụng những cổng không theo mặc định.

---

## 9. DNS, DHCP và Default Gateway

### 9.1. DNS – Domain Name System

DNS là hệ thống phân giải tên miền thành địa chỉ IP.

Ví dụ:

`www.example.com → IP Address`

Nhờ có DNS, người dùng không cần ghi nhớ địa chỉ IP của từng website mà chỉ cần sử dụng tên miền.

### 9.2. DHCP – Dynamic Host Configuration Protocol

DHCP là giao thức giúp tự động cấp phát cấu hình mạng cho thiết bị, bao gồm:

- Địa chỉ IP.
- Subnet Mask.
- Default Gateway.
- DNS Server.

### 9.3. Quá trình DHCP DORA

Quá trình cấp phát địa chỉ IP thông qua DHCP thường gồm 4 bước:

1. **Discover:** Client tìm kiếm DHCP Server.
2. **Offer:** DHCP Server gửi đề nghị cấp phát cấu hình IP.
3. **Request:** Client gửi yêu cầu sử dụng cấu hình được đề nghị.
4. **Acknowledge:** DHCP Server xác nhận việc cấp phát cấu hình.

### 9.4. Default Gateway – Cổng mặc định

Default Gateway là thiết bị chuyển tiếp lưu lượng từ mạng cục bộ đến các mạng khác.

Ví dụ:

Trong mạng gia đình, Router thường đóng vai trò là Default Gateway để chuyển tiếp dữ liệu từ thiết bị trong nhà ra Internet.

---

## 10. Điều gì xảy ra khi truy cập một website?

Khi người dùng nhập địa chỉ:

`https://www.example.com`

Trình duyệt sẽ thực hiện một chuỗi hoạt động để kết nối đến máy chủ.

### Các bước cơ bản

1. Thiết bị kiểm tra xem đã biết địa chỉ IP của máy chủ hay chưa.
2. Nếu cần thiết, DNS phân giải tên miền thành địa chỉ IP.
3. Thiết bị xác định địa chỉ đích nằm trong mạng cục bộ hay mạng khác.
4. Nếu đích nằm ngoài mạng cục bộ, gói tin được gửi đến Default Gateway.
5. TCP thiết lập kết nối với máy chủ, thường sử dụng cổng 443.
6. TLS hỗ trợ thiết lập kết nối HTTPS được mã hóa.
7. Trình duyệt gửi HTTP Request đến máy chủ.
8. Máy chủ phản hồi bằng HTTP Response.
9. Trình duyệt xử lý dữ liệu và hiển thị trang web.

---

## 11. Bài tập củng cố

### Bài tập 1: OSI và TCP/IP

1. Tầng nào trong mô hình OSI chịu trách nhiệm định tuyến?
2. Tầng nào sử dụng TCP và UDP?
3. Địa chỉ MAC thuộc tầng nào?
4. Tầng nào trong mô hình TCP/IP bao gồm DNS và HTTP?

### Bài tập 2: Địa chỉ IP

Cho:

- IP Address: `192.168.10.25`
- Subnet Mask: `255.255.255.0`

Hãy xác định:

1. Địa chỉ mạng (Network Address).
2. Phần địa chỉ host (Host Portion).
3. Đây là địa chỉ IPv4 riêng hay công cộng?
4. CIDR tương ứng là bao nhiêu?

### Bài tập 3: TCP và UDP

1. Sự khác biệt chính giữa TCP và UDP là gì?
2. Ba bước trong quá trình TCP Three-Way Handshake là gì?
3. Giao thức nào phù hợp khi cần truyền dữ liệu tin cậy và đúng thứ tự?
4. Vì sao một ứng dụng có thể lựa chọn UDP thay vì TCP?

### Bài tập 4: DNS, DHCP và ARP

Giải thích chức năng của:

1. DNS.
2. DHCP.
3. ARP.

Sau đó, mô tả ngắn gọn quá trình xảy ra khi một thiết bị kết nối Wi-Fi và truy cập một website lần đầu tiên.

### Bài tập 5: Các cổng mạng

Ghép mỗi cổng với giao thức phổ biến tương ứng:

| Port | Protocol |
|---|---|
| 22 | ? |
| 53 | ? |
| 80 | ? |
| 443 | ? |
| 67/68 | ? |

---

## 12. Tổng kết

- Mạng máy tính cho phép các thiết bị giao tiếp và chia sẻ tài nguyên.
- Mô hình OSI có 7 tầng, trong khi TCP/IP thường được trình bày với 4 tầng.
- Địa chỉ IP hỗ trợ định địa chỉ logic và định tuyến.
- Địa chỉ MAC được sử dụng trong giao tiếp mạng cục bộ.
- TCP cung cấp khả năng truyền dữ liệu tin cậy, đúng thứ tự; UDP có chi phí xử lý thấp hơn.
- DNS phân giải tên miền, DHCP cấp cấu hình mạng và ARP tìm địa chỉ MAC tương ứng với IPv4.
- Port giúp xác định điểm cuối của dịch vụ hoặc ứng dụng.
- Default Gateway chuyển tiếp lưu lượng đến các mạng khác.

---

## 13. Tài liệu tham khảo

- [Cloudflare Learning - What is a Computer Network?](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-network/)
- [Cloudflare Learning - What is TCP/IP?](https://www.cloudflare.com/learning/ddos/glossary/tcp-ip/)
- [Cloudflare Learning - What is DNS?](https://www.cloudflare.com/learning/dns/what-is-dns/)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [RFC 9293 - Transmission Control Protocol (TCP)](https://www.rfc-editor.org/rfc/rfc9293)

---

## 14. Đáp án và giải thích

### Bài tập 1: OSI và TCP/IP

1. **Network Layer (Tầng mạng – Layer 3):** Chịu trách nhiệm định địa chỉ logic và định tuyến gói tin giữa các mạng.
2. **Transport Layer (Tầng giao vận – Layer 4):** Sử dụng TCP và UDP để giao tiếp giữa các tiến trình.
3. **Data Link Layer (Tầng liên kết dữ liệu – Layer 2):** Sử dụng địa chỉ MAC để truyền frame trong mạng cục bộ.
4. **Application Layer (Tầng ứng dụng):** Bao gồm các giao thức như DNS và HTTP trong mô hình TCP/IP.

### Bài tập 2: Địa chỉ IP

Cho:

- IP Address: `192.168.10.25`
- Subnet Mask: `255.255.255.0`

Đáp án:

1. **Network Address:** `192.168.10.0`
2. **Host Portion:** `25`
3. **Loại địa chỉ:** Private IPv4.
4. **CIDR:** `/24`

**Giải thích:** Subnet Mask `255.255.255.0` tương ứng với `/24`, nghĩa là 24 bit đầu tiên xác định mạng và 8 bit còn lại xác định host. Địa chỉ `192.168.10.25` thuộc dải địa chỉ riêng `192.168.0.0/16`.

### Bài tập 3: TCP và UDP

1. **Sự khác biệt chính:** TCP đảm bảo dữ liệu được truyền tin cậy và đúng thứ tự, trong khi UDP không đảm bảo dữ liệu đến nơi hoặc đúng thứ tự.
2. **Ba bước bắt tay TCP:** `SYN → SYN-ACK → ACK`.
3. **Giao thức phù hợp:** TCP.
4. **Lý do sử dụng UDP:** UDP có chi phí xử lý thấp hơn và phù hợp với các ứng dụng ưu tiên độ trễ thấp như gọi thoại, phát trực tuyến và trò chơi trực tuyến.

### Bài tập 4: DNS, DHCP và ARP

1. **DNS:** Chuyển đổi tên miền thành địa chỉ IP.
2. **DHCP:** Tự động cấp cấu hình mạng như địa chỉ IP, Subnet Mask, Default Gateway và DNS Server.
3. **ARP:** Tìm địa chỉ MAC tương ứng với một địa chỉ IPv4 trong mạng cục bộ.

**Quá trình khi thiết bị kết nối Wi-Fi và truy cập website:**

1. Thiết bị kết nối vào mạng Wi-Fi.
2. DHCP cấp cấu hình mạng cho thiết bị.
3. DNS phân giải tên miền website thành địa chỉ IP.
4. Thiết bị xác định địa chỉ đích nằm trong mạng cục bộ hay mạng khác.
5. Nếu đích nằm ngoài mạng cục bộ, thiết bị gửi dữ liệu đến Default Gateway.
6. ARP có thể được sử dụng để tìm địa chỉ MAC của Gateway.
7. Thiết bị thiết lập kết nối TCP và, nếu sử dụng HTTPS, thiết lập kết nối TLS.
8. Trình duyệt gửi HTTP Request và nhận HTTP Response từ máy chủ.

### Bài tập 5: Các cổng mạng

| Port | Giao thức |
|---|---|
| 22 | SSH |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 67/68 | DHCP |

**Giải thích:** Số cổng xác định điểm cuối của dịch vụ hoặc ứng dụng. Đây là những cổng được sử dụng phổ biến, nhưng một dịch vụ vẫn có thể sử dụng cổng khác.
