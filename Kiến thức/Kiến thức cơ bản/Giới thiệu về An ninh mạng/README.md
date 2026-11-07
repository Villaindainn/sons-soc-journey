# 01 - Giới thiệu về An ninh mạng (Cybersecurity Introduction)

## 1. Tổng quan

Bài học này giới thiệu những kiến thức nền tảng về an ninh mạng, các nguyên tắc bảo mật cơ bản và những khái niệm quan trọng thường gặp trong lĩnh vực bảo mật thông tin.

### Mục tiêu bài học

- Hiểu an ninh mạng là gì và tại sao nó quan trọng.
- Nắm được ba nguyên tắc cốt lõi của bảo mật thông tin (CIA Triad).
- Phân biệt được các khái niệm Threat, Vulnerability và Risk.
- Nhận biết một số hình thức tấn công mạng phổ biến.
- Biết các biện pháp cơ bản để bảo vệ hệ thống và dữ liệu.
- Hiểu sơ lược về các vai trò trong lĩnh vực an ninh mạng.

---

## 2. An ninh mạng là gì?

**An ninh mạng (Cybersecurity)** là lĩnh vực tập trung vào việc bảo vệ hệ thống máy tính, mạng, thiết bị và dữ liệu khỏi các hành vi truy cập trái phép, phá hoại, đánh cắp hoặc làm gián đoạn hoạt động.

### Các mục tiêu của an ninh mạng

- Bảo vệ thông tin khỏi việc bị truy cập trái phép.
- Đảm bảo dữ liệu không bị sửa đổi hoặc phá hoại trái phép.
- Đảm bảo hệ thống và dịch vụ luôn sẵn sàng khi cần thiết.
- Phát hiện, ngăn chặn và xử lý các sự cố bảo mật.

### Những tài sản cần được bảo vệ

| Tài sản | Ví dụ |
|---|---|
| Dữ liệu (Data) | Thông tin cá nhân, mật khẩu, dữ liệu khách hàng |
| Phần cứng (Hardware) | Máy chủ, máy tính, thiết bị mạng |
| Phần mềm (Software) | Hệ điều hành, ứng dụng, cơ sở dữ liệu |
| Tài khoản (Accounts) | Tài khoản người dùng, tài khoản quản trị |
| Hệ thống mạng (Network) | Router, switch, hệ thống mạng nội bộ |

---

## 3. Bộ ba nguyên tắc bảo mật CIA Triad

**CIA Triad** là mô hình gồm ba nguyên tắc cốt lõi trong bảo mật thông tin: Confidentiality, Integrity và Availability.

### 3.1. Confidentiality – Tính bảo mật

Đảm bảo thông tin chỉ được truy cập bởi những người hoặc hệ thống có quyền.

**Ví dụ:**
- Sử dụng mật khẩu để bảo vệ tài khoản.
- Mã hóa dữ liệu để người không có quyền không thể đọc được nội dung.
- Phân quyền để nhân viên chỉ truy cập được dữ liệu cần thiết.

### 3.2. Integrity – Tính toàn vẹn

Đảm bảo dữ liệu không bị sửa đổi, xóa hoặc làm sai lệch trái phép.

**Ví dụ:**
- Bảo vệ điểm số sinh viên khỏi việc bị thay đổi trái phép.
- Sử dụng hàm băm (Hash Function) để kiểm tra tính toàn vẹn của tệp.
- Ghi nhật ký các thao tác chỉnh sửa dữ liệu.

### 3.3. Availability – Tính sẵn sàng

Đảm bảo hệ thống và dữ liệu có thể được truy cập khi người dùng hợp lệ cần sử dụng.

**Ví dụ:**
- Sao lưu dữ liệu định kỳ.
- Sử dụng nhiều máy chủ để giảm nguy cơ gián đoạn.
- Bảo vệ website khỏi tấn công từ chối dịch vụ (DoS/DDoS).

### 3.4. Bảng tổng hợp CIA Triad

| Nguyên tắc | Ý nghĩa | Ví dụ vi phạm |
|---|---|---|
| Confidentiality | Bảo mật thông tin | Kẻ tấn công đánh cắp dữ liệu cá nhân |
| Integrity | Đảm bảo tính toàn vẹn | Điểm số bị chỉnh sửa trái phép |
| Availability | Đảm bảo tính sẵn sàng | Website bị DDoS và không thể truy cập |

---

## 4. Threat, Vulnerability và Risk

Đây là ba khái niệm quan trọng thường xuyên xuất hiện trong lĩnh vực an ninh mạng.

### 4.1. Threat – Mối đe dọa

**Threat** là một yếu tố hoặc sự kiện có khả năng gây hại cho hệ thống, dữ liệu hoặc tài sản.

Ví dụ:
- Hacker cố gắng xâm nhập máy chủ.
- Phần mềm độc hại (Malware).
- Nhân viên vô tình làm lộ thông tin.
- Thiên tai gây hư hỏng trung tâm dữ liệu.

### 4.2. Vulnerability – Lỗ hổng bảo mật

**Vulnerability** là điểm yếu trong hệ thống, phần mềm, cấu hình hoặc quy trình mà kẻ tấn công có thể khai thác.

Ví dụ:
- Sử dụng mật khẩu yếu.
- Phần mềm chưa được cập nhật bản vá.
- Cấu hình máy chủ sai.
- Ứng dụng tồn tại lỗ hổng SQL Injection.

### 4.3. Risk – Rủi ro

**Risk** là khả năng xảy ra thiệt hại khi một mối đe dọa khai thác lỗ hổng và gây ảnh hưởng đến tài sản.

Ví dụ:

Một máy chủ đang sử dụng phiên bản phần mềm cũ có lỗ hổng bảo mật.

- **Threat:** Kẻ tấn công muốn xâm nhập máy chủ.
- **V
