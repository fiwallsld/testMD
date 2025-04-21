# 📄 TÊN CHỨC NĂNG / MODULE
> VisaProfile

---

## 🧭 1. Mục đích

1. Chức năng này dùng để quản lý thông tin hồ sơ xin visa của người dùng, bao gồm: thông tin cá nhân, các loại giấy tờ, hình ảnh liên quan được lưu dạng ảnh hoặc file PDF,
thông tin về đất nước muốn xin Visa, lịch sử xin visa, lịch sử đi du lịch của khách hàng.
2. Quản lý bộ Hồ sơ visa cho các Tour.
3. Phân luồng cập nhật, xét duyệt Hồ sơ theo vai trò Sale -> TeamLead/GroupLead -> OP;

---

## 🏗 2. Cấu trúc hệ thống

| Thành phần      | Tên File / Class             | Ghi chú                        |
|------------------|-----------------------------|--------------------------------|
| Route            | `/visaProfile/`             | Các endpoint API liên quan     |
| Controller       | `VisaProfileController`     | Điều phối logic                |
| Request          | `StoreVisaProfileRequest`   | Validate dữ liệu đầu vào       |
| Service          | `VisaProfileService`        | Chứa business logic chính      |
| Repository       | `VisaProfileRepository`     | Truy xuất dữ liệu DB           |
| Model            | `VisaProfile`               | Eloquent model chính           |
| Model            | `DeclarationVisa`           | Eloquent model Tờ khai visa    |
| Model            | `TravelHistory`             | Eloquent model Lịch sử du lịch |
| Model            | `CustomerRelation`          | Eloquent model Người liên quan |
| Model            | `Groups`                    | Eloquent model Tên bộ hồ sơ    |
| Model            | `VisaProfileFiles`          | Eloquent model File liên quan  |
| Model            | `Tour, Market, Booking`     | Eloquent model File liên quan  |
| Model            | `Nations`                   | Eloquent model File liên quan  |
| cats             | `booleanCats` `datetimeCast`| Cats để auto xử lý modal       |
| cats             | `commaCats` `jsonCat`       | Cats để auto xử lý modal       |

---

## 🔄 3. Luồng xử lý chính

### ➕ Tạo mới:
1. Gửi request từ frontend với thông tin và file đính kèm.
2. Validate dữ liệu tại `StoreVisaProfileRequest`.
3. Gọi xử lý tại `VisaProfileController::store()`.
  3.1. Tạo mã code
  3.2. Tạo các modal liên quan (DeclarationVisa, TravelHistory, Group, CustomerRelation)
  3.3. Xử lý file liên quan
4. Đưa ra thông báo cho người dùng liên quan.
5. Trả về kết quả dưới dạng JSON.

### ✏️ Cập nhật:
1. Gửi request từ frontend với thông tin và file đính kèm.
2. Validate dữ liệu tại `StoreVisaProfileRequest`.
3. Gọi xử lý tại `VisaProfileController::update()`.
  3.1. Tạo mã code
  3.2. Tạo các modal liên quan (DeclarationVisa, TravelHistory, Group, CustomerRelation)
  3.3. Xử lý file liên quan
4. Đưa ra thông báo cho người dùng liên quan.
5. Trả về kết quả dưới dạng JSON.

### 🗑️ Xóa:
1. Xác minh quyền xóa.
2. Gọi `VisaProfileService::destroy()` => soft delete.
3. Xóa tài tất cả tài liệu, modal con liên quan đến HSVS.

---

### listview
1. Gửi request từ FE lên với các trường filter

## 📤 4. Request đầu vào (Sample)

\`\`\`json
POST /api/visa-profiles

{
  "full_name": "Nguyen Van A",
  "passport_number": "B12345678",
  "visa_type": "Tourist",
  "start_date": "2025-01-01",
  "end_date": "2025-03-01",
  "documents": [FILE_UPLOAD]
}
\`\`\`

---

## 📥 5. Response đầu ra (Sample)

\`\`\`json
{
  "success": true,
  "data": {
    "id": 123,
    "full_name": "Nguyen Van A",
    "passport_number": "B12345678",
    "visa_type": "Tourist",
    "documents": [
      {
        "name": "passport_scan.jpg",
        "url": "https://example.com/uploads/passport_scan.jpg"
      }
    ]
  }
}
\`\`\`

---

## 📁 6. Upload file

- Hỗ trợ các định dạng: `image`, `pdf`
- Số lượng tối đa: 12
- Thư mục lưu trữ: `/storage/public/visaprofile/`
- Các hình ảnh có thông tin liên quan đến các nội dung cần nhập như PP,CCCD, BHXH thì vừa xử lý upload vừa show preview bên dưới các ô nhập nội dung tương ứng bằng cách sử dụng icon-preview.

---

## ⚠️ 7. Các case đặc biệt đã xử lý

- Trường họ tên là bắt buộc
- Ngày bắt đầu phải nhỏ hơn ngày kết thúc, các ngày trong điều kiện check với today
- Upload sai định dạng hoặc file lỗi
- Rollback khi upload thất bại

---

## 📎 8. Tài liệu liên quan

- Migration DB: `C:\laragon\www\QuanLySanPham\database\migrations\visaprofile\update.md`
Chứa tất cả các nội dung khởi tạo, cập nhật DB

