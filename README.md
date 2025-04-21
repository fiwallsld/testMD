# 🔁 Flow Tính Năng: Quy trình xử lý Hồ sơ Visa Nhật Bản

## 🧭 Tổng Quan Quy Trình

> Dưới đây là quy trình chuẩn từ khi khách hàng cung cấp thông tin ban đầu đến khi hoàn tất kết quả visa. Luồng dữ liệu được hiển thị cụ thể qua sơ đồ và giải thích chi tiết ở từng bước nghiệp vụ bên dưới.

![Quy trình nhận khách Nhật Bản](./photo_2024-11-28_13-22-47.jpg)

---

## 1️⃣ SALE TẠO HỒ SƠ KHÁCH HÀNG

### 🎯 Mục đích
Khởi tạo hồ sơ visa với thông tin sơ bộ ban đầu của khách hàng, làm cơ sở xử lý và duyệt sau này.

### 🧾 Quy trình tạo hồ sơ:

- **Bắt buộc nhập:**
  - `full_name`: Tên khách hàng — bắt buộc nhập.
  - Được chuẩn hóa và validate trước khi gọi:
    ```php
    VisaProfile::create($request);
    ```

- **Thông tin bổ sung khác:**
  - `market_id`: Thị trường khách hàng đến từ đâu.
  - `tour_id`: Tour khách hàng đang tham gia.
  - `booking_id`: Mã booking tương ứng với tour.
  - `op_visa_id`: Lấy theo `tour_id` tự động gán từ tour.
  - `truong_doan_id`: Lấy theo trưởng đoàn tương ứng với tour.

---

## 2️⃣ PHÊ DUYỆT NỘI DUNG (GL/TL)

### 🧾 Mục đích
Khi hồ sơ được SALE cập nhật, toàn bộ nội dung sẽ được đẩy lên để các vai trò kiểm duyệt tương ứng (Team Leader hoặc Group Leader).

- Yêu cầu phê duyệt được sinh tự động thông qua hệ thống notification nội bộ sau mỗi cập nhật hồ sơ.
- Mỗi phần nội dung cần kiểm duyệt đều có thể ghi chú lại nội dung phản hồi hoặc yêu cầu bổ sung.

---

## 3️⃣ THÔNG BÁO TOÀN CỤC VISA

### 📣 Hệ thống thông báo chung cho toàn bộ thành viên liên quan đến bộ hồ sơ visa.

- Tự động gửi đến:  
  `SALE`, `GL`, `TL`, `OP` — những người có liên quan đến hồ sơ.

- Thông báo sinh theo định nghĩa từ `visaprofile_fields`, bao gồm:
  - Trường nào thay đổi
  - Ai là người thay đổi
  - Ghi chú nội dung thay đổi
  - Thời gian cập nhật

- Các loại thông báo:
  - Yêu cầu duyệt
  - Bổ sung thông tin
  - Hoàn tất hồ sơ
  - Kết quả visa

---

## ✅ Kết Luận

- Mỗi hành động cập nhật từ người dùng đều phải sinh log và bắn thông báo liên quan để đảm bảo tiến trình xuyên suốt.
- Mỗi người trong quy trình đều có quyền kiểm tra lại tiến độ hồ sơ nhờ vào luồng xử lý thông báo và dữ liệu chuẩn hóa.
