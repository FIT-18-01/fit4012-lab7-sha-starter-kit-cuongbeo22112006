# FIT4012 Lab 7 - Báo cáo 1 trang: SHA-256

## 1. Mục tiêu / Objective

Mục tiêu của bài thực hành là phân tích cấu trúc C++ của thuật toán SHA-256, thực hiện các bước padding/chia khối/tạo message schedule/vòng nén, áp dụng SHA-256 để kiểm tra toàn vẹn file, mô phỏng lưu mật khẩu bằng hash và cải thiện bằng salt.

## 2. Cách làm / Approach

- Dùng `make` để biên dịch 4 chương trình: `sha256`, `file_integrity`, `password_hash`, `salted_password_hash`.
- Chạy self-test `./sha256 --self-test` và kiểm thử giá trị hash cho chuỗi mẫu.
- Tạo file mẫu, tính hash bằng `./sha256 --hash-file`, rồi dùng `./file_integrity sample.txt <expected_hash>` để kiểm tra trước và sau khi sửa file.
- Dùng `./password_hash register <file>` để lưu hash mật khẩu và `./password_hash login <password> <file>` để xác thực đúng/sai.
- Dùng `./salted_password_hash register <file>` để lưu salt + hash và `./salted_password_hash login <password> <file>` để xác thực.

## 3. Kết quả / Result

- Hash của chuỗi `abc`:
  `ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad`
- Hash của file mẫu trước khi sửa (`FIT4012 SHA file integrity sample\n`):
  `5ee62dc925a9958dbd6732c570a23c7f65a8c11066e889b15068cfb4bf1a0bd9`
- Kết quả kiểm tra file trước khi sửa: `[PASS]` (hash khớp với giá trị kỳ vọng).
- Kết quả kiểm tra file sau khi sửa nội dung (`tampered\n`): `[FAIL]` (hash khác, phát hiện tamper).
- Kết quả đăng nhập với mật khẩu đúng: thành công.
- Kết quả đăng nhập với mật khẩu sai: bị từ chối.
- Hai bản ghi `salt:hash` của cùng một mật khẩu có khác nhau không? Có, cùng một mật khẩu nhưng salt khác nhau tạo ra các bản ghi khác nhau.

## 4. Kết luận / Conclusion

- SHA-256 là hàm băm một chiều, output cố định 256 bit và rất nhạy với thay đổi dữ liệu. Do đó nó phù hợp để kiểm tra toàn vẹn file: bất kỳ sửa đổi nhỏ nào cũng thay đổi digest.
- Salt cần thiết khi lưu mật khẩu vì cùng một mật khẩu nếu không có salt sẽ tạo ra hash giống nhau, dễ bị tấn công bằng rainbow table hoặc so sánh hash.
- Trong hệ thống xác thực thật, SHA-256 thuần túy vẫn chưa đủ vì nó quá nhanh và không có cơ chế làm chậm. Cần dùng các hàm băm mật khẩu chuyên dụng như bcrypt/scrypt/Argon2id cùng với salt và stretching.
