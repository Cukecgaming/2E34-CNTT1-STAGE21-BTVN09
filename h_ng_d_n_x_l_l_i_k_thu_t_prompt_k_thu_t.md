# HƯỚNG DẪN XỬ LÝ LỖI & KỸ THUẬT PROMPT KỸ THUẬT (VOLTCHARGE SYSTEM)

## 📌 MỤC TIÊU DỰ ÁN

Tài liệu này được thiết kế nhằm chuẩn hóa quy trình vận hành và xử lý sự cố cho hệ thống Kiosk nạp năng lượng thông minh **VoltCharge**. Đồng thời, tài liệu cung cấp khung chuẩn để kỹ thuật viên biến các Trợ lý Trí tuệ Nhân tạo (ChatGPT, Gemini) thành người hỗ trợ kỹ thuật 24/7 an toàn và chính xác.

## 🛠️ CÔNG THỨC PROMPT 3 THÀNH PHẦN (3-PART PROMPT FORMULA)

Khi gặp lỗi hệ thống, tuyệt đối **không đặt câu hỏi chung chung** (tránh hiện tượng **AI Hallucination** - AI ảo giác và đoán mò). Luôn áp dụng công trúc 3 thành phần chuẩn kỹ sư:

```
────────────────────────────────────────────────────────┐
│ 1. CONTEXT (NGỮ CẢNH)                                  │
│ - Mô tả thiết bị, hệ điều hành, tác vụ đang thực hiện  │
├────────────────────────────────────────────────────────┤
│ 2. RAW ERROR (LỖI NGUYÊN VÂN)                          │
│ - Trích xuất 100% nhật ký lỗi tiếng Anh từ Terminal    │
┌├────────────────────────────────────────────────────────┤
│ 3. INSTRUCTION (YÊU CẦU CHI TIẾT)                     │
│ - Yêu cầu dịch, giải thích và đưa ra các bước xử lý    │
└────────────────────────────────────────────────────────┘ 
```

### 

## 🔍 GIẢI MÃ LỖI `System.UnauthorizedAccessException` (Exit Code 5)

### 1. Ý nghĩa tiếng Việt đời thường:

Ứng dụng trạm sạc cố gắng ghi lịch sử phiên làm việc vào tệp `session.log` nằm trong thư mục hệ thống `C:\Program Files\VoltCharge\data\`. Tuy nhiên, Windows 11 đã **chặn hành động này** vì ứng dụng đang chạy dưới quyền người dùng thông thường, không có thẩm quyền ghi dữ liệu vào vùng bảo vệ của hệ thống.

### 2. Nguyên nhân gốc rễ:

* **Mã lỗi Exit Code 5 (`0x00000005`)**: Quy định lỗi phân quyền NTFS trên hệ điều hành Windows (**Access Denied**).

* Thư mục `C:\Program Files` mặc định yêu cầu quyền Quản trị viên (**Administrator**) để sửa đổi hoặc tạo mới các tệp tin.

## 🛠️ 3 BƯỚC KHẮC PHỤC SỰ CỐ TẠI CHỖ (WINDOWS 11)

### Bước 1: Khởi chạy tạm thời với quyền Administrator

1. Tìm tệp tin thực thi `ControllerDaemon.exe` trong thư mục cài đặt `C:\Program Files\VoltCharge\`.

2. Nhấp chuột phải vào tệp và chọn **Run as administrator** (Chạy với quyền quản trị viên).

3. Kiểm tra xem trạm sạc đã nhận diện và nạp điện lại bình thường chưa.

### Bước 2: Cấu hình chạy quyền Admin cố định

1. Nhấp chuột phải vào `ControllerDaemon.exe` $\rightarrow$ chọn **Properties**.

2. Chuyển sang thẻ **Compatibility** (Tương thích).

3. Tích chọn mục **Run this program as an administrator**

4. Nhấn **Apply** $\rightarrow$ **OK** để lưu lại.

### Bước 3: Phân quyền ghi (Write Permission) cho thư mục `data`

Mở ứng dụng PowerShell với quyền Administrator và chạy câu lệnh sau để cấp quyền ghi cho ứng dụng:

```
icacls "C:\Program Files\VoltCharge\data" /grant Users:(OI)(CI)F /T

```

## 🛡️ 3 NGUYÊN TẮC BẢO MẬT VÀNG KHI DÙNG AI TRONG DOANH NGHIỆP

1. **Tuyệt đối không gửi Secret / API Key / Mật khẩu lên AI:**

   * Trước khi copy-paste nhật ký lỗi vào AI, phải tiến hành **Masking/Redact** loại bỏ các chuỗi nhạy cảm.

   * *Ví dụ:* Thay thế `API_KEY: volt_secret_token_889922` thành `API_KEY: [REDACTED_SECRET]`.

   * **Lý do:** Dữ liệu đẩy lên các mô hình AI công cộng có thể bị lưu trữ, sử dụng để huấn luyện mô hình hoặc nguy cơ rò rỉ nếu tài khoản bị xâm nhập.

2. **Chống ảo giác AI (Zero Trust AI Output):**

   * Không bao giờ thực thi mù quáng các câu lệnh can thiệp sâu hệ thống (như `rmdir`, `format`, chỉnh sửa Registry) do AI gợi ý nếu chưa hiểu rõ bản chất lệnh.

3. **Cung cấp ngữ cảnh đầy đủ:**

   * AI phản hồi chính xác hay đoán mò phụ thuộc hoàn toàn vào lượng thông tin kỹ thuật nguyên văn bạn cung cấp trong Prompt.