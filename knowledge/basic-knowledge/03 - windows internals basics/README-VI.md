🌐 **Ngôn ngữ:** [English](README.md) | [Tiếng Việt](README-VI.md)

# 03 - Kiến thức cơ bản về Windows Internals (Windows Internals Basics)

## 1. Tổng quan

Bài học này giới thiệu những kiến thức nền tảng về Windows Internals và cách hệ điều hành Windows quản lý tiến trình, dịch vụ, cấu hình hệ thống, hệ thống tệp và nhật ký sự kiện.

Hiểu về Windows Internals là kiến thức quan trọng đối với những người làm trong lĩnh vực An toàn thông tin, đặc biệt là các vị trí SOC Analyst, Digital Forensics, Incident Response và Malware Analysis.

### Mục tiêu bài học

Sau khi hoàn thành bài học, bạn cần có khả năng:

- Hiểu về Process, Thread và Process ID trong Windows.
- Giải thích mối quan hệ giữa Parent Process và Child Process.
- Hiểu Windows Services và các chế độ khởi động dịch vụ.
- Sử dụng Windows Event Viewer để kiểm tra nhật ký hệ thống và bảo mật.
- Nhận biết các Windows Event ID quan trọng.
- Hiểu cấu trúc và chức năng của Windows Registry.
- Hiểu hệ thống tệp NTFS và các dữ liệu quan trọng trong điều tra số.
- Sử dụng các lệnh Windows và PowerShell cơ bản để kiểm tra hoạt động hệ thống.

---

## 2. Kiến trúc hệ điều hành Windows (Windows Operating System Architecture)

Windows là hệ điều hành chịu trách nhiệm quản lý tài nguyên phần cứng, thực thi ứng dụng, kiểm soát quyền truy cập tài nguyên và cung cấp các dịch vụ cho người dùng.

### 2.1. User Mode và Kernel Mode

Windows chia quá trình thực thi thành hai chế độ truy cập chính.

| Chế độ | Mô tả | Ví dụ |
|---|---|---|
| User Mode | Chạy ứng dụng với quyền truy cập tài nguyên hệ thống bị giới hạn | Trình duyệt, Notepad, PowerShell |
| Kernel Mode | Chạy các thành phần hệ điều hành với quyền truy cập đặc quyền | Windows Kernel, Device Drivers |

**User Mode – Chế độ người dùng**

Các ứng dụng thông thường hoạt động trong User Mode.

Ứng dụng chạy trong User Mode không thể trực tiếp truy cập tùy ý vào bộ nhớ Kernel hoặc phần cứng.

Thay vào đó, ứng dụng yêu cầu hệ điều hành cung cấp dịch vụ thông qua các giao diện được hỗ trợ.

**Kernel Mode – Chế độ nhân**

Kernel Mode cung cấp quyền truy cập đặc quyền vào tài nguyên hệ thống.

Các nhiệm vụ quan trọng bao gồm:

- Quản lý bộ nhớ (Memory Management).
- Lập lịch tiến trình và luồng (Process and Thread Scheduling).
- Tương tác với phần cứng thông qua Driver.
- Quản lý tài nguyên hệ thống.
- Thực thi các cơ chế bảo mật của hệ điều hành.

### 2.2. Mô hình kiến trúc Windows đơn giản

```text
+----------------------------------+
|          User Applications       |
|  Browser | Notepad | PowerShell  |
+----------------------------------+
|        Windows API / DLLs        |
+----------------------------------+
|         System Call Layer        |
+----------------------------------+
|          Windows Kernel          |
| Process | Memory | I/O | Drivers |
+----------------------------------+
|             Hardware             |
|       CPU | RAM | Storage        |
+----------------------------------+
```

Đây là mô hình khái quát đơn giản, không phải sơ đồ đầy đủ của kiến trúc Windows.

### 2.3. Tại sao cần hiểu kiến trúc Windows?

Hiểu sự khác biệt giữa User Mode và Kernel Mode giúp chuyên viên bảo mật điều tra:

- Hoạt động bất thường của tiến trình.
- Các hành vi leo thang đặc quyền (Privilege Escalation).
- Driver độc hại.
- Hành vi bất thường của hệ thống.
- Mã độc cố gắng truy cập các tài nguyên được bảo vệ.

---

## 3. Tiến trình trong Windows (Windows Processes)

### 3.1. Process là gì?

**Process (Tiến trình)** là một phiên bản chương trình đang được hệ điều hành thực thi.

Khi người dùng mở một ứng dụng, Windows sẽ tạo hoặc sử dụng một Process để thực thi các chỉ thị của chương trình đó.

Ví dụ:

| Ứng dụng | Tên Process thường gặp |
|---|---|
| File Explorer | `explorer.exe` |
| Notepad | `notepad.exe` |
| Command Prompt | `cmd.exe` |
| PowerShell | `powershell.exe` |
| Task Manager | `Taskmgr.exe` |

Một Process chứa hoặc tham chiếu đến nhiều tài nguyên quan trọng.

| Thành phần | Mô tả |
|---|---|
| PID | Mã định danh của tiến trình |
| Parent PID | Mã định danh tiến trình cha được ghi nhận |
| Virtual Address Space | Không gian địa chỉ bộ nhớ ảo của tiến trình |
| Threads | Các luồng thực thi bên trong tiến trình |
| Handles | Các tham chiếu đến đối tượng của hệ điều hành |
| Access Token | Thông tin ngữ cảnh bảo mật của tiến trình |

### 3.2. Process ID (PID)

**Process ID (PID)** là mã số định danh được Windows cấp cho một tiến trình.

Ví dụ:

```text
Process Name       PID
----------------------
explorer.exe       4120
notepad.exe        5864
cmd.exe            7208
```

Windows sử dụng PID để xác định và quản lý các tiến trình.

Đặc điểm quan trọng:

- Mỗi tiến trình đang tồn tại có một PID.
- PID không phải mã định danh vĩnh viễn.
- Windows có thể sử dụng lại PID sau khi tiến trình trước đó kết thúc.
- Nên kết hợp PID với những thông tin khác như thời điểm tạo tiến trình để xác định chính xác hoạt động.

### 3.3. Parent Process và Child Process

Một tiến trình có thể tạo ra một tiến trình khác.

- **Parent Process (Tiến trình cha):** Tiến trình thực hiện việc tạo một tiến trình khác.
- **Child Process (Tiến trình con):** Tiến trình được tạo ra bởi một tiến trình khác.

Ví dụ:

```text
explorer.exe
    |
    +--- notepad.exe
    |
    +--- cmd.exe
           |
           +--- whoami.exe
```

Trong ví dụ trên:

- `explorer.exe` tạo ra `notepad.exe` và `cmd.exe`.
- `cmd.exe` tạo ra `whoami.exe`.
- `cmd.exe` là Parent Process của `whoami.exe`.

Mối quan hệ Parent–Child giúp tái dựng hoạt động thực thi chương trình trên hệ thống.

**Ví dụ trong An toàn thông tin**

Xét cây tiến trình sau:

```text
winword.exe
    |
    +--- powershell.exe
              |
              +--- suspicious.exe
```

Hành vi này cần được kiểm tra thêm vì một ứng dụng soạn thảo văn bản có liên quan đến việc khởi chạy PowerShell và một tệp thực thi không rõ nguồn gốc.

Tuy nhiên, quan hệ Parent–Child bất thường không đồng nghĩa chắc chắn với mã độc.

Chuyên viên bảo mật cần kiểm tra:

- Dòng lệnh thực thi (Process Command Line).
- Đường dẫn tệp thực thi.
- Chữ ký số (Digital Signature).
- Thời điểm tạo tiến trình.
- Hoạt động của người dùng.
- Các sự kiện bảo mật liên quan.

**Lưu ý:** Thông tin Parent Process trên Windows có thể bị ảnh hưởng bởi cơ chế tạo tiến trình. Không nên xem thông tin này là bằng chứng tuyệt đối về chương trình thực sự khởi tạo một hành động.

### 3.4. Thread là gì?

**Thread (Luồng)** là đơn vị thực thi bên trong một Process.

Một Process có thể chứa nhiều Thread.

Ví dụ, một trình duyệt có thể sử dụng nhiều Thread để:

- Xử lý thao tác của người dùng.
- Thực hiện các tác vụ nền.
- Xử lý hoạt động mạng.
- Thực thi các tác vụ của ứng dụng.

### 3.5. So sánh Process và Thread

| Đặc điểm | Process | Thread |
|---|---|---|
| Khái niệm | Một chương trình đang thực thi | Đơn vị thực thi bên trong Process |
| Bộ nhớ | Có không gian địa chỉ ảo riêng | Chia sẻ không gian địa chỉ của Process |
| Tài nguyên | Sở hữu hoặc tham chiếu tài nguyên hệ thống | Sử dụng tài nguyên của Process |
| Thực thi | Chứa một hoặc nhiều Thread | Thực thi các chỉ thị chương trình |
| Định danh | PID | TID (Thread ID) |

### 3.6. Kiểm tra các Process đang chạy

**Cách 1: Sử dụng Task Manager**

1. Nhấn `Ctrl + Shift + Esc`.
2. Mở tab Processes.
3. Quan sát các ứng dụng và tiến trình nền đang chạy.
4. Mở tab Details.
5. Kiểm tra tên tiến trình và PID.

**Cách 2: Sử dụng Command Prompt**

Liệt kê các tiến trình đang chạy:

```cmd
tasklist
```

Tìm một tiến trình cụ thể:

```cmd
tasklist | findstr notepad
```

**Cách 3: Sử dụng PowerShell**

Liệt kê các tiến trình:

```powershell
Get-Process
```

Tìm tiến trình Notepad:

```powershell
Get-Process -Name notepad
```

Hiển thị tên Process, PID và Parent PID:

```powershell
Get-CimInstance Win32_Process |
Select-Object Name, ProcessId, ParentProcessId
```

Hiển thị đường dẫn thực thi và dòng lệnh nếu có:

```powershell
Get-CimInstance Win32_Process |
Select-Object Name, ProcessId, ExecutablePath, CommandLine
```

Một số thông tin tiến trình có thể yêu cầu quyền quản trị viên.

---

## 4. Dịch vụ Windows (Windows Services)

### 4.1. Windows Service là gì?

**Windows Service (Dịch vụ Windows)** là một chương trình hoặc thành phần phần mềm được thiết kế để cung cấp chức năng chạy nền.

Các dịch vụ được quản lý bởi **Service Control Manager (SCM)**.

Nhiều dịch vụ có thể khởi động mà không cần người dùng mở ứng dụng trực tiếp.

Ví dụ:

| Dịch vụ | Chức năng |
|---|---|
| Windows Update (`wuauserv`) | Hỗ trợ cập nhật Windows |
| DHCP Client (`Dhcp`) | Quản lý cấu hình mạng DHCP |
| DNS Client (`Dnscache`) | Cung cấp chức năng DNS Client |
| Print Spooler (`Spooler`) | Quản lý hoạt động in ấn |
| Windows Event Log (`EventLog`) | Quản lý nhật ký sự kiện Windows |

### 4.2. So sánh Process và Service

| Process | Service |
|---|---|
| Một chương trình đang thực thi | Một dịch vụ nền được quản lý |
| Có PID | Khi hoạt động sẽ chạy trong một Process |
| Có thể có giao diện đồ họa | Thường hoạt động không cần giao diện người dùng |
| Có thể được khởi chạy bởi ứng dụng hoặc người dùng | Thường được quản lý thông qua SCM |

Một Process có thể chứa nhiều Service.

Ví dụ, `svchost.exe` được sử dụng để lưu trữ và chạy nhiều dịch vụ Windows.

### 4.3. Các chế độ khởi động Service (Startup Types)

Windows hỗ trợ nhiều chế độ khởi động dịch vụ.

| Startup Type | Mô tả |
|---|---|
| Automatic | Tự động khởi động khi hệ thống khởi động |
| Automatic (Delayed Start) | Tự động khởi động sau một khoảng thời gian |
| Manual | Khởi động khi được người dùng, ứng dụng hoặc hệ thống yêu cầu |
| Disabled | Không thể khởi động cho đến khi cấu hình được thay đổi |

### 4.4. Các trạng thái của Service (Service States)

Một số trạng thái phổ biến:

- Running: Đang chạy.
- Stopped: Đã dừng.
- Start Pending: Đang chờ khởi động.
- Stop Pending: Đang chờ dừng.
- Paused: Đang tạm dừng.

### 4.5. Quản lý Windows Services

**Cách 1: Sử dụng Services Console**

1. Nhấn `Win + R`.
2. Nhập:

```text
services.msc
```

3. Chọn một dịch vụ.
4. Kiểm tra trạng thái và chế độ khởi động.
5. Mở Properties để xem cấu hình chi tiết.

**Cách 2: Sử dụng Command Prompt**

Kiểm tra một dịch vụ:

```cmd
sc query Spooler
```

Xem cấu hình dịch vụ:

```cmd
sc qc Spooler
```

**Cách 3: Sử dụng PowerShell**

Liệt kê các dịch vụ:

```powershell
Get-Service
```

Tìm dịch vụ Windows Update:

```powershell
Get-Service -Name wuauserv
```

Liệt kê các dịch vụ đang chạy:

```powershell
Get-Service |
Where-Object {$_.Status -eq "Running"}
```

### 4.6. Windows Services trong An toàn thông tin

Windows Services có thể bị lợi dụng làm cơ chế duy trì quyền truy cập (Persistence).

Một chương trình độc hại có thể cố gắng cài đặt một Service để tự động khởi động cùng Windows.

Những dấu hiệu cần điều tra:

- Dịch vụ mới được cài đặt.
- Đường dẫn tệp thực thi bất thường.
- Tên dịch vụ không rõ nguồn gốc.
- Dịch vụ chạy với quyền quá cao.
- Dịch vụ liên quan đến tệp thực thi không đáng tin cậy.

Cần kiểm tra thêm bằng chứng thay vì kết luận một dịch vụ là độc hại chỉ dựa vào tên của nó.

---

## 5. Windows Event Viewer

### 5.1. Event Viewer là gì?

**Windows Event Viewer** là công cụ tích hợp trong Windows, được sử dụng để kiểm tra các nhật ký sự kiện do thành phần hệ thống và ứng dụng tạo ra.

Event Logs lưu lại thông tin về hoạt động hệ thống, lỗi, sự kiện bảo mật và nhiều hoạt động khác.

Event Viewer rất quan trọng trong:

- Khắc phục sự cố hệ thống (System Troubleshooting).
- Giám sát bảo mật (Security Monitoring).
- Ứng phó sự cố (Incident Response).
- Điều tra số (Digital Forensics).
- Điều tra hoạt động đáng ngờ.

### 5.2. Mở Event Viewer

Nhấn `Win + R` và nhập:

```text
eventvwr.msc
```

Hoặc tìm kiếm Event Viewer trong Start Menu.

### 5.3. Các nhóm nhật ký Windows chính

| Log | Mô tả |
|---|---|
| Application | Sự kiện được tạo bởi ứng dụng |
| Security | Các sự kiện kiểm toán bảo mật |
| System | Sự kiện từ thành phần Windows và dịch vụ |
| Setup | Sự kiện cài đặt và cấu hình |
| Forwarded Events | Sự kiện được chuyển tiếp từ máy tính khác |

### 5.4. Application Log

Application Log chứa các sự kiện được tạo bởi phần mềm.

Ví dụ:

- Ứng dụng bị treo hoặc gặp sự cố.
- Lỗi ứng dụng.
- Cảnh báo phần mềm.
- Thông tin chẩn đoán ứng dụng.

### 5.5. System Log

System Log chứa các sự kiện được tạo bởi thành phần hệ điều hành.

Ví dụ:

- Driver gặp lỗi.
- Cài đặt dịch vụ.
- Dịch vụ gặp sự cố khi khởi động.
- Lỗi liên quan đến phần cứng.

### 5.6. Security Log

Security Log ghi nhận các sự kiện liên quan đến kiểm toán bảo mật.

Ví dụ:

- Đăng nhập thành công.
- Đăng nhập thất bại.
- Sự kiện tạo Process.
- Hoạt động quản lý tài khoản.
- Xóa nhật ký kiểm toán.

**Lưu ý:** Các sự kiện bảo mật phụ thuộc vào chính sách kiểm toán (Audit Policy) được cấu hình. Không phải mọi sự kiện đều được ghi lại mặc định.

### 5.7. Các mức độ sự kiện (Event Levels)

| Event Level | Mô tả |
|---|---|
| Critical | Sự cố hệ thống nghiêm trọng |
| Error | Một hoạt động gặp lỗi |
| Warning | Cảnh báo về vấn đề tiềm ẩn |
| Information | Thông tin về hoạt động |
| Verbose | Thông tin chẩn đoán chi tiết |

Một sự kiện Error hoặc Warning không nhất thiết là dấu hiệu của một cuộc tấn công.

### 5.8. Các thuộc tính quan trọng của Event

Khi điều tra một sự kiện, cần chú ý:

| Thuộc tính | Mô tả |
|---|---|
| Event ID | Mã định danh sự kiện |
| Time Created | Thời điểm sự kiện được ghi nhận |
| Log Name | Nhật ký chứa sự kiện |
| Source / Provider | Thành phần tạo ra sự kiện |
| Level | Mức độ của sự kiện |
| Computer | Máy tính liên quan đến sự kiện |
| Event Data | Dữ liệu chi tiết của sự kiện |

Cần kết hợp Event ID với nguồn tạo sự kiện và loại Log để phân tích chính xác.

---

## 6. Các Windows Event ID quan trọng

### 6.1. Các Security Event ID phổ biến

| Event ID | Ý nghĩa | Log |
|---|---|---|
| 4624 | Đăng nhập thành công | Security |
| 4625 | Đăng nhập thất bại | Security |
| 4634 | Tài khoản đăng xuất | Security |
| 4648 | Đăng nhập bằng thông tin xác thực được cung cấp tường minh | Security |
| 4672 | Gán các đặc quyền đặc biệt cho một phiên đăng nhập mới | Security |
| 4688 | Tạo tiến trình mới | Security |
| 4720 | Tạo tài khoản người dùng | Security |
| 4726 | Xóa tài khoản người dùng | Security |
| 1102 | Xóa Security Audit Log | Security |

### 6.2. System Event ID phổ biến

| Event ID | Ý nghĩa | Log |
|---|---|---|
| 7045 | Cài đặt một Windows Service mới | System |

### 6.3. Event ID 4624 – Successful Logon

Event ID `4624` cho biết một phiên đăng nhập đã được tạo thành công.

Các trường thông tin quan trọng có thể bao gồm:

- Account Name.
- Account Domain.
- Logon Type.
- Source Network Address.
- Workstation Name.
- Logon ID.

**Các Logon Type phổ biến**

| Logon Type | Ý nghĩa |
|---|---|
| 2 | Đăng nhập trực tiếp trên máy tính (Interactive Logon) |
| 3 | Đăng nhập qua mạng (Network Logon) |
| 4 | Đăng nhập theo lô (Batch Logon) |
| 5 | Đăng nhập dịch vụ (Service Logon) |
| 7 | Mở khóa máy tính (Unlock) |
| 10 | Đăng nhập từ xa, thường liên quan đến RDP |
| 11 | Đăng nhập tương tác bằng thông tin xác thực được lưu trong bộ nhớ đệm |

### 6.4. Event ID 4625 – Failed Logon

Event ID `4625` cho biết một lần đăng nhập thất bại.

Chuyên viên bảo mật có thể kiểm tra các lần đăng nhập thất bại liên tiếp để phát hiện hoạt động dò mật khẩu.

Ví dụ:

```text
10:00:01 - Event ID 4625
10:00:03 - Event ID 4625
10:00:05 - Event ID 4625
10:00:07 - Event ID 4625
```

Nhiều lần đăng nhập thất bại trong một khoảng thời gian ngắn có thể là dấu hiệu đáng ngờ.

Tuy nhiên, nguyên nhân cũng có thể đến từ mật khẩu lưu sai hoặc lỗi cấu hình.

### 6.5. Event ID 4688 – Process Creation

Event ID `4688` ghi nhận việc tạo một Process mới khi chính sách kiểm toán tương ứng được bật.

Các thông tin hữu ích có thể bao gồm:

- New Process Name.
- New Process ID.
- Creator Process Name.
- Creator Process ID.
- Subject Account.
- Process Command Line, nếu đã bật chính sách kiểm toán dòng lệnh.

Sự kiện này giúp tái dựng hoạt động thực thi tiến trình trong quá trình điều tra.

### 6.6. Event ID 7045 – Service Installation

Event ID `7045` thường được tạo ra khi một Windows Service mới được cài đặt.

Thông tin có thể bao gồm:

- Service Name.
- Service File Name.
- Service Type.
- Service Start Type.
- Service Account.

Sự kiện này hữu ích khi điều tra các cơ chế duy trì quyền truy cập đáng ngờ.

### 6.7. Event ID 1102 – Audit Log Cleared

Event ID `1102` cho biết Security Audit Log đã bị xóa.

Đây là sự kiện cần chú ý vì kẻ tấn công đôi khi cố gắng xóa nhật ký để che giấu hoạt động.

Tuy nhiên, quản trị viên cũng có thể xóa nhật ký vì mục đích bảo trì hợp pháp.

### 6.8. Lọc các sự kiện trong Event Viewer

**Sử dụng Event Viewer**

1. Mở Event Viewer.
2. Truy cập Windows Logs.
3. Chọn Security.
4. Nhấn Filter Current Log.
5. Nhập các Event ID cần tìm.

Ví dụ:

```text
4624,4625,4688,1102
```

**Sử dụng PowerShell**

Tìm các lần đăng nhập thành công:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4624
} -MaxEvents 10
```

Tìm các lần đăng nhập thất bại:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4625
} -MaxEvents 10
```

Tìm các dịch vụ mới được cài đặt:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'System'
    Id = 7045
} -MaxEvents 10
```

Một số truy vấn có thể yêu cầu quyền Administrator.

---

## 7. Windows Registry

### 7.1. Windows Registry là gì?

**Windows Registry** là cơ sở dữ liệu có cấu trúc phân cấp, được sử dụng để lưu trữ các thiết lập cấu hình của Windows, phần cứng, hồ sơ người dùng và ứng dụng.

Registry chứa các thông tin như:

- Cấu hình hệ điều hành.
- Thiết lập phần mềm đã cài đặt.
- Tùy chọn của người dùng.
- Cấu hình dịch vụ.
- Cấu hình khởi động hệ thống.

### 7.2. Mở Registry Editor

Nhấn `Win + R` và nhập:

```text
regedit
```

**Cảnh báo:** Thay đổi Registry không đúng cách có thể khiến hệ thống hoạt động không ổn định. Cần sao lưu các thiết lập liên quan trước khi chỉnh sửa.

### 7.3. Các Registry Hive chính

| Registry Hive | Mô tả |
|---|---|
| HKEY_LOCAL_MACHINE (HKLM) | Cấu hình trên toàn hệ thống |
| HKEY_CURRENT_USER (HKCU) | Cấu hình của người dùng hiện tại |
| HKEY_CLASSES_ROOT (HKCR) | Thông tin liên kết tệp và đăng ký COM |
| HKEY_USERS (HKU) | Cấu hình hồ sơ người dùng đã được nạp |
| HKEY_CURRENT_CONFIG (HKCC) | Cấu hình phần cứng hiện tại |

### 7.4. Cấu trúc Registry

Ví dụ:

```text
HKEY_LOCAL_MACHINE
    |
    +--- SOFTWARE
           |
           +--- Microsoft
                  |
                  +--- Windows
```

Các thành phần của Registry:

- **Key:** Một vùng chứa có thể bao gồm Subkey và Value.
- **Subkey:** Key nằm bên trong một Key khác.
- **Value:** Một mục dữ liệu có tên.
- **Value Data:** Nội dung dữ liệu được lưu trong Value.

### 7.5. Các kiểu dữ liệu Registry phổ biến

| Type | Mô tả |
|---|---|
| REG_SZ | Chuỗi văn bản |
| REG_DWORD | Số nguyên 32-bit |
| REG_QWORD | Số nguyên 64-bit |
| REG_BINARY | Dữ liệu nhị phân |
| REG_MULTI_SZ | Nhiều chuỗi ký tự |
| REG_EXPAND_SZ | Chuỗi có thể mở rộng biến môi trường |

### 7.6. Các Registry Key liên quan đến khởi động

Một số Registry Key được sử dụng để cấu hình chương trình tự khởi chạy khi người dùng đăng nhập.

Ví dụ:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

Các vị trí này rất quan trọng khi điều tra cơ chế duy trì quyền truy cập (Persistence).

Tuy nhiên, nhiều ứng dụng hợp pháp cũng sử dụng những Registry Key này.

### 7.7. Kiểm tra thông tin Registry

Sử dụng Command Prompt:

```cmd
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

Sử dụng PowerShell:

```powershell
Get-ItemProperty `
"HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"
```

Các lệnh trên chỉ đọc thông tin Registry và không thay đổi dữ liệu.

---

## 8. Hệ thống tệp Windows (Windows File System)

### 8.1. File System là gì?

**File System (Hệ thống tệp)** xác định cách dữ liệu được tổ chức, lưu trữ, truy cập và quản lý trên thiết bị lưu trữ.

Windows hỗ trợ nhiều hệ thống tệp khác nhau.

| File System | Mô tả |
|---|---|
| NTFS | Hệ thống tệp phổ biến trên Windows |
| FAT32 | Tương thích rộng, giới hạn kích thước mỗi tệp dưới 4 GiB |
| exFAT | Thường được sử dụng trên thiết bị lưu trữ di động |
| ReFS | Hệ thống tệp được thiết kế để tăng độ tin cậy và khả năng mở rộng trong các môi trường được hỗ trợ |

### 8.2. NTFS – New Technology File System

NTFS là hệ thống tệp được sử dụng phổ biến trên các ổ đĩa hệ thống Windows.

Các tính năng quan trọng:

- Phân quyền truy cập tệp và thư mục.
- Access Control Lists.
- Ghi nhật ký hệ thống tệp (File System Journaling).
- Nén tệp.
- Encrypting File System (EFS).
- Alternate Data Streams (ADS).
- Hỗ trợ tệp và phân vùng có dung lượng lớn.

### 8.3. NTFS Permissions

NTFS Permissions kiểm soát quyền truy cập vào tệp và thư mục.

| Permission | Mô tả |
|---|---|
| Read | Đọc nội dung tệp |
| Write | Ghi dữ liệu |
| Read & Execute | Đọc và thực thi tệp |
| Modify | Đọc, ghi, chỉnh sửa và xóa |
| Full Control | Bao gồm quản lý quyền truy cập và các khả năng liên quan đến quyền sở hữu |

NTFS sử dụng Access Control List (ACL) để xác định người dùng và nhóm nào được phép truy cập tài nguyên.

### 8.4. Master File Table (MFT)

**Master File Table (MFT)** là một cấu trúc quan trọng của NTFS.

MFT chứa các bản ghi mô tả các tệp và thư mục trên phân vùng NTFS.

Các bản ghi MFT có thể chứa:

- Tên tệp.
- Thuộc tính tệp.
- Thông tin kích thước tệp.
- Dấu thời gian.
- Tham chiếu đến dữ liệu tệp.
- Tham chiếu đến các bản ghi thư mục liên quan.

**Ví dụ trong Digital Forensics**

Điều tra viên có thể phân tích các bản ghi MFT để xác định:

- Các tệp đã tồn tại trên phân vùng.
- Metadata của tệp.
- Các bản ghi liên quan đến tệp có thể đã bị xóa.
- Hoạt động của hệ thống tệp có liên quan đến Timeline.

Khả năng thu thập thông tin phụ thuộc vào trạng thái hiện tại của hệ thống tệp và các bản ghi còn tồn tại.

### 8.5. File Metadata

**File Metadata (Siêu dữ liệu tệp)** là thông tin mô tả các thuộc tính của một tệp.

Các ví dụ phổ biến:

| Metadata | Mô tả |
|---|---|
| File Name | Tên tệp |
| File Size | Kích thước tệp |
| Creation Time | Thời điểm tạo được ghi nhận |
| Modified Time | Thời điểm chỉnh sửa gần nhất được ghi nhận |
| Access Time | Thời điểm truy cập được ghi nhận |
| File Attributes | Các thuộc tính của tệp |

Metadata rất hữu ích trong việc xây dựng Timeline trong Digital Forensics.

Tuy nhiên, dấu thời gian có thể bị thay đổi, bị ảnh hưởng bởi hoạt động hệ thống hoặc bị thao túng có chủ đích.

### 8.6. Kiểm tra File Metadata

Sử dụng PowerShell:

```powershell
Get-Item "C:\Windows\notepad.exe" |
Select-Object Name, Length, CreationTime, LastWriteTime, LastAccessTime
```

Ví dụ kết quả:

```text
Name          : notepad.exe
Length        : ...
CreationTime  : ...
LastWriteTime : ...
LastAccessTime: ...
```

Giá trị thực tế phụ thuộc vào hệ thống Windows đang sử dụng.

---

## 9. Windows Internals trong An toàn thông tin

Hiểu Windows Internals giúp chuyên viên bảo mật điều tra các hành vi đáng ngờ trên hệ thống.

### 9.1. Điều tra Process (Process Investigation)

Ví dụ:

```text
explorer.exe
    |
    +--- powershell.exe
              |
              +--- unknown.exe
```

Các câu hỏi cần điều tra:

1. Tiến trình nào đã khởi chạy PowerShell?
2. Lệnh nào đã được thực thi?
3. Tệp `unknown.exe` nằm ở đâu?
4. Tệp thực thi có chữ ký số hợp lệ không?
5. Có sự kiện Event ID 4688 liên quan không?

### 9.2. Điều tra Service đáng ngờ

Ví dụ:

Một dịch vụ mới xuất hiện với đường dẫn tệp thực thi không quen thuộc.

Các bước điều tra:

1. Xác định tên dịch vụ.
2. Kiểm tra đường dẫn tệp thực thi.
3. Kiểm tra Startup Type.
4. Xác định tài khoản chạy dịch vụ.
5. Tìm Event ID 7045 trong System Log.
6. Đối chiếu thời điểm cài đặt với các sự kiện khác.

### 9.3. Điều tra đăng nhập thất bại

Ví dụ:

```text
Event ID 4625
Event ID 4625
Event ID 4625
Event ID 4624
```

Nhiều lần đăng nhập thất bại sau đó xuất hiện một lần đăng nhập thành công có thể cần được điều tra.

Chuyên viên bảo mật nên kiểm tra:

- Account Name.
- Logon Type.
- Source IP Address nếu có.
- Thời điểm xảy ra sự kiện.
- Các sự kiện xác thực liên quan.

Chỉ riêng chuỗi sự kiện này chưa đủ để chứng minh một cuộc tấn công đã xảy ra.

### 9.4. Điều tra Persistence thông qua Registry

Ví dụ:

Một tệp thực thi không rõ nguồn gốc được tham chiếu trong Registry Run Key.

Các bước điều tra:

1. Xác định đường dẫn Registry.
2. Ghi lại Value Name và Value Data.
3. Kiểm tra tệp thực thi được tham chiếu.
4. Kiểm tra Metadata và Digital Signature.
5. Đối chiếu với hoạt động Process và Event Log.

---

## 10. Bài tập củng cố

### Bài tập 1: Process và Parent Process

1. Process và Thread khác nhau như thế nào?
2. PID có chức năng gì?
3. Parent Process là gì?
4. Tại sao mối quan hệ Parent–Child hữu ích trong điều tra bảo mật?
5. Lệnh PowerShell nào có thể hiển thị tên Process và Parent PID?

### Bài tập 2: Windows Services

1. Windows Service là gì?
2. Service Control Manager có vai trò gì?
3. Automatic và Manual khác nhau như thế nào?
4. Lệnh nào dùng để mở giao diện quản lý Services?
5. Tại sao một dịch vụ tự động khởi động nhưng không rõ nguồn gốc cần được kiểm tra?

### Bài tập 3: Event Viewer

1. Event Viewer có chức năng gì?
2. System Log và Security Log khác nhau như thế nào?
3. Event ID nào biểu thị đăng nhập thành công?
4. Event ID nào biểu thị đăng nhập thất bại?
5. Event ID nào ghi nhận việc tạo Process khi đã bật kiểm toán?
6. Event ID nào biểu thị một dịch vụ mới được cài đặt?

### Bài tập 4: Windows Registry

1. Windows Registry là gì?
2. HKLM và HKCU khác nhau như thế nào?
3. Registry Key là gì?
4. Registry Run Key có chức năng gì?
5. Tại sao cần cẩn thận khi chỉnh sửa Registry?

### Bài tập 5: Windows File System

1. NTFS là gì?
2. Master File Table có chức năng gì?
3. NTFS Permissions được sử dụng để làm gì?
4. File Metadata là gì?
5. Tại sao dấu thời gian của tệp hữu ích trong Digital Forensics?

### Bài tập 6: Tình huống điều tra bảo mật

Xét chuỗi hoạt động sau:

```text
09:00:01 - Event ID 4625
09:00:03 - Event ID 4625
09:00:05 - Event ID 4625
09:00:10 - Event ID 4624
09:00:15 - Event ID 4688
09:00:20 - Event ID 7045
```

Trả lời các câu hỏi:

1. Điều gì xảy ra lúc 09:00:01?
2. Điều gì xảy ra lúc 09:00:10?
3. Event ID 4688 biểu thị điều gì?
4. Event ID 7045 biểu thị điều gì?
5. Tại sao chuỗi sự kiện này cần được điều tra thêm?
6. Cần thu thập thêm bằng chứng nào trước khi kết luận có một cuộc tấn công xảy ra?

---

## 11. Tổng kết

- Windows phân chia hoạt động thực thi ứng dụng và các thao tác đặc quyền thông qua User Mode và Kernel Mode.
- Process là một chương trình đang được thực thi.
- Thread là đơn vị thực thi bên trong Process.
- PID là mã định danh của một Process trong thời gian nó tồn tại.
- Quan hệ Parent–Child giúp tái dựng hoạt động thực thi chương trình.
- Windows Services cung cấp các chức năng chạy nền và được quản lý bởi Service Control Manager.
- Event Viewer được sử dụng để kiểm tra nhật ký sự kiện Windows.
- Security Log chứa các sự kiện kiểm toán bảo mật.
- System Log chứa các sự kiện liên quan đến hệ điều hành và dịch vụ.
- Event ID giúp xác định các loại sự kiện cụ thể.
- Windows Registry lưu trữ cấu hình hệ thống và ứng dụng.
- NTFS cung cấp chức năng quản lý tệp, phân quyền và cấu trúc metadata.
- Master File Table là một thành phần quan trọng trong điều tra hệ thống tệp NTFS.
- Điều tra bảo mật cần kết hợp nhiều nguồn bằng chứng thay vì chỉ dựa vào một sự kiện đơn lẻ.

---

## 12. Tài liệu tham khảo

- [Microsoft Learn – Windows Internals](https://learn.microsoft.com/en-us/sysinternals/resources/windows-internals)
- [Microsoft Learn – Windows Processes and Threads](https://learn.microsoft.com/en-us/windows/win32/procthread/processes-and-threads)
- [Microsoft Learn – Windows Services](https://learn.microsoft.com/en-us/windows/win32/services/services)
- [Microsoft Learn – Windows Event Log](https://learn.microsoft.com/en-us/windows/win32/wes/windows-event-log)
- [Microsoft Learn – Windows Security Auditing](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/)
- [Microsoft Learn – Windows Registry](https://learn.microsoft.com/en-us/windows/win32/sysinfo/registry)
- [Microsoft Learn – NTFS Overview](https://learn.microsoft.com/en-us/windows-server/storage/file-server/ntfs-overview)
- [Microsoft Sysinternals](https://learn.microsoft.com/en-us/sysinternals/)

---

## 13. Đáp án và giải thích

### Bài tập 1: Process và Parent Process

1. **Process và Thread:** Process là một chương trình đang thực thi, trong khi Thread là đơn vị thực thi bên trong Process.
2. **PID:** Là mã số định danh được cấp cho một Process trong thời gian nó tồn tại.
3. **Parent Process:** Là tiến trình tạo ra một tiến trình khác.
4. **Ứng dụng trong điều tra:** Mối quan hệ Parent–Child giúp chuyên viên bảo mật tái dựng chuỗi thực thi và phát hiện hoạt động bất thường.
5. **Lệnh PowerShell:**

```powershell
Get-CimInstance Win32_Process |
Select-Object Name, ProcessId, ParentProcessId
```

### Bài tập 2: Windows Services

1. **Windows Service:** Là dịch vụ chạy nền cung cấp chức năng cho Windows hoặc ứng dụng.
2. **Service Control Manager:** Quản lý quá trình cài đặt, cấu hình, khởi động và điều khiển dịch vụ.
3. **Automatic và Manual:** Automatic tự động khởi động cùng hệ thống, còn Manual khởi động khi có yêu cầu.
4. **Services Console:** `services.msc`.
5. **Dịch vụ không rõ nguồn gốc:** Có thể là phần mềm trái phép hoặc cơ chế Persistence, nhưng cũng có thể là dịch vụ hợp pháp cần xác minh.

### Bài tập 3: Event Viewer

1. **Event Viewer:** Công cụ dùng để kiểm tra nhật ký sự kiện Windows.
2. **System và Security:** System Log ghi nhận sự kiện hệ điều hành, còn Security Log ghi nhận các sự kiện kiểm toán bảo mật.
3. **Đăng nhập thành công:** Event ID `4624`.
4. **Đăng nhập thất bại:** Event ID `4625`.
5. **Tạo Process:** Event ID `4688`.
6. **Cài đặt Service:** Event ID `7045`, thường nằm trong System Log.

### Bài tập 4: Windows Registry

1. **Windows Registry:** Cơ sở dữ liệu phân cấp chứa các thiết lập cấu hình của Windows và ứng dụng.
2. **HKLM và HKCU:** HKLM lưu cấu hình toàn hệ thống, còn HKCU lưu cấu hình của người dùng hiện tại.
3. **Registry Key:** Là vùng chứa có thể bao gồm các Subkey và Value.
4. **Run Keys:** Là các vị trí Registry có thể cấu hình ứng dụng tự khởi chạy khi người dùng đăng nhập.
5. **An toàn:** Thay đổi không đúng có thể khiến ứng dụng hoặc hệ điều hành hoạt động không ổn định.

### Bài tập 5: Windows File System

1. **NTFS:** Là hệ thống tệp của Windows hỗ trợ phân quyền, journaling, metadata và nhiều tính năng khác.
2. **MFT:** Lưu trữ các bản ghi mô tả tệp và thư mục trên phân vùng NTFS.
3. **NTFS Permissions:** Kiểm soát người dùng và nhóm nào được phép truy cập tệp và thư mục.
4. **File Metadata:** Là thông tin mô tả tệp, bao gồm kích thước, thuộc tính và dấu thời gian.
5. **Giá trị trong điều tra:** Dấu thời gian giúp xây dựng Timeline nhưng cần được xác minh bằng các bằng chứng khác.

### Bài tập 6: Tình huống điều tra bảo mật

1. **09:00:01:** Ghi nhận một lần đăng nhập thất bại (Event ID 4625).
2. **09:00:10:** Ghi nhận một lần đăng nhập thành công (Event ID 4624).
3. **Event ID 4688:** Một Process mới được tạo.
4. **Event ID 7045:** Một Windows Service mới được cài đặt.
5. **Lý do điều tra:** Nhiều lần đăng nhập thất bại, sau đó đăng nhập thành công, tạo Process và cài đặt Service có thể phù hợp với một hoạt động đáng ngờ.
6. **Bằng chứng bổ sung:** Kiểm tra tên tài khoản, địa chỉ nguồn, Logon Type, dòng lệnh Process, đường dẫn tệp thực thi, cấu hình Service, dấu thời gian và các nhật ký liên quan.

**Lưu ý quan trọng:** Những sự kiện này chưa chứng minh chúng thuộc cùng một người dùng hoặc cùng một chuỗi tấn công. Cần xác minh mối liên hệ giữa chúng trước khi đưa ra kết luận.
