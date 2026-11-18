# BÁO CÁO THỰC HÀNH LAB 2 - AN TOÀN VÀ BẢO MẬT THÔNG TIN

## THÔNG TIN SINH VIÊN
- **Họ và tên:** NGUYỄN TRỌNG THỂ
- **Mã số sinh viên (MSSV):** 1150080117
- **Chủ đề bài Lab:** LAB 2 - MÃ HÓA ĐỐI XỨNG HIỆN ĐẠI
- **Repository GitHub:** [Sith004/LAB2_1150080117_NGUYENTRONGTHE](https://github.com/Sith004/LAB2_1150080117_NGUYENTRONGTHE)

---

## 1. CẤU TRÚC THƯ MỤC NỘP BÀI

```text
├── LAB2_MaHoaHienDai/               # Dự án Java Maven (NetBeans)
│   ├── pom.xml                      # Cấu hình phụ thuộc (mssql-jdbc, BouncyCastle bcprov)
│   └── src/main/java/week_02/       # Mã nguồn Java
│       ├── CipherUtil.java          # Tiện ích byte, Base64, Hex, SecureRandom, PBKDF2
│       ├── FileTextIO.java          # Tiện ích đọc/ghi file text UTF-8
│       ├── DbConfig.java            # Quản lý kết nối JDBC SQL Server an toàn
│       ├── HistoryDao.java          # DAO đọc/ghi bảng dbo.crypto_history
│       ├── UserDao.java             # DAO xác thực, PBKDF2 + salt, bảng dbo.app_users
│       ├── DESCipher.java           # Thuật toán DES/CBC/PKCS5Padding + IV ngẫu nhiên
│       ├── frm_DES.java (.form)     # Giao diện kiểm thử DES
│       ├── TripleDESCipher.java     # Thuật toán TripleDES (DESede/CBC/PKCS5Padding)
│       ├── frm_TripleDES.java (.form) # Giao diện kiểm thử TripleDES
│       ├── AESCipher.java           # Thuật toán AES-256-GCM + PBKDF2
│       ├── frm_AES.java (.form)     # Giao diện AES, Đăng ký, Đăng nhập, Cấp vé Ticket
│       ├── RC4Cipher.java           # Thuật toán mã dòng RC4 (KSA + PRGA)
│       ├── frm_RC4.java (.form)     # Giao diện kiểm thử RC4 (hiển thị Hex)
│       ├── TwofishCipher.java       # Thuật toán Twofish (BouncyCastle) CBC + PKCS7
│       ├── frm_Twofish.java (.form) # Giao diện Twofish (nhập/tạo IV, lưu file)
│       ├── AESCTRCipher.java        # Thuật toán AES-256 chế độ CTR (Counter)
│       ├── frm_AES_CTR.java (.form) # Giao diện AES-CTR (sinh khóa, sinh nonce)
│       ├── frm_KetNoiSQL.java (.form) # Giao diện cấu hình & Test Connection SQL Server
│       ├── frm_LichSu.java (.form)  # Giao diện xem lịch sử thao tác từ SQL Server
│       └── frm_Menu.java (.form)    # Cửa sổ chính Menu điều hướng toàn bài
├── SQL/                             # Các tệp kịch bản CSDL SQL Server
│   ├── 01_Tao_CSDL.sql              # Script tạo CSDL LAB2_MaHoaHienDai và 2 bảng
│   ├── 02_Tao_Tai_Khoan_SQL.sql     # Script tạo login lab2_user và cấp quyền
│   └── 03_Kiem_Tra_Du_Lieu.sql      # Script truy vấn kiểm tra dữ liệu bảng
├── LAB2_Python/                     # Bài tập nâng cao 02 (Python)
│   └── aes_cbc_gui.py               # Giao diện Tkinter AES-256-CBC có tự kiểm thử
├── BaoCao_Lab2_1150080117_NguyenTrongThe.docx # Báo cáo chi tiết & hình ảnh minh chứng kết quả
└── README.md                        # Hướng dẫn chi tiết và thông tin sinh viên
```

---

## 2. YÊU CẦU MÔI TRƯỜNG & PHẦN MỀM

- **Java Development Kit (JDK):** JDK 17 hoặc JDK 21.
- **IDE:** Apache NetBeans (bản 20 trở lên).
- **Cơ sở dữ liệu:** Microsoft SQL Server (SQLEXPRESS hoặc MSSQLSERVER) + SSMS.
  - Cổng TCP/IP: `14333`
  - Chế độ xác thực: `SQL Server and Windows Authentication mode` (Mixed Mode).
  - Tài khoản Lab: `lab2_user` / Mật khẩu: `LabUser@2026`.
- **Python:** Python 3.10+ (cần thư viện `cryptography`).

---

## 3. HƯỚNG DẪN CÀI ĐẶT & CHẠY CHƯƠNG TRÌNH

### Bước 1: Thiết lập Cơ sở dữ liệu (SQL Server)
1. Mở **SQL Server Management Studio (SSMS)** và kết nối bằng Windows Authentication.
2. Mở file `SQL/01_Tao_CSDL.sql` và bấm **Execute (F5)** để tạo CSDL `LAB2_MaHoaHienDai` cùng 2 bảng: `dbo.app_users`, `dbo.crypto_history`.
3. Mở file `SQL/02_Tao_Tai_Khoan_SQL.sql` và bấm **Execute (F5)** để tạo người dùng `lab2_user` với mật khẩu `LabUser@2026`.
4. Đảm bảo cổng mạng `14333` đã được kích hoạt trong **SQL Server Configuration Manager**.

### Bước 2: Chạy ứng dụng Java NetBeans
1. Mở Apache NetBeans $\rightarrow$ **File** $\rightarrow$ **Open Project...** $\rightarrow$ Chọn thư mục `LAB2_MaHoaHienDai`.
2. Chuột phải vào tên Project $\rightarrow$ chọn **Clean and Build** (`Shift + F11`) để Maven tải các phụ thuộc:
   - `mssql-jdbc` (kết nối SQL Server).
   - `bcprov-jdk18on` (thư viện BouncyCastle cho thuật toán Twofish).
3. Bấm phím **`F6`** (hoặc chuột phải project $\rightarrow$ chọn **Run**) để mở cửa sổ Menu chính `frm_Menu`.
4. Từ `frm_Menu`:
   - Bấm **Kết nối SQL Server** $\rightarrow$ Nhập mật khẩu `LabUser@2026` $\rightarrow$ bấm **Test Connection**.
   - Trải nghiệm từng bài thực hành từ `2.1` đến `2.6`.
   - Bấm **Xem lịch sử** để kiểm tra lịch sử thao tác được lưu tự động trên SQL Server.

### Bước 3: Chạy chương trình Python (Bài nâng cao 02 - AES-CBC)
1. Cài đặt thư viện:
   ```cmd
   py -m pip install cryptography
   ```
2. Chạy tự kiểm thử (Self-test):
   ```cmd
   cd LAB2_Python
   py aes_cbc_gui.py --self-test
   ```
3. Khởi chạy giao diện Tkinter:
   ```cmd
   py aes_cbc_gui.py
   ```

---

## 4. KẾT QUẢ KIỂM THỬ BẮT BUỘC (BẢNG 2.1 TRONG TÀI LIỆU)

| STT | Bài thực hành | Dữ liệu kiểm thử | Kết quả đạt được | Trạng thái |
|:---:|:---|:---|:---|:---:|
| 1 | **2.1 DES** | Plaintext: *"Xin chào Lab 2 - Mã hóa hiện đại!"*, Key: `12345678` | $Decrypt(Encrypt(x)) = x$. Mỗi lần Encrypt sinh IV ngẫu nhiên ra bản mã khác nhau. | **ĐẠT (PASS)** |
| 2 | **2.2 TripleDES** | Key đúng 24 ký tự: `123456789012345678901234` | Giải mã chính xác 100%. Thử Key 23 ký tự phát hiện và chặn lỗi đúng chuẩn. | **ĐẠT (PASS)** |
| 3 | **2.3 AES** | Đăng ký tài khoản mới + Đăng nhập | Đăng nhập thành công, cấp Ticket ngẫu nhiên 32 ký tự HEX. Bảng `app_users` và `crypto_history` ghi nhận dòng mới. | **ĐẠT (PASS)** |
| 4 | **2.4 RC4** | Plaintext: `Plaintext`, Key: `Key` | Bản mã HEX sinh ra chính xác tuyệt đối: **`BBF316E8D940AF0AD3`**. | **ĐẠT (PASS)** |
| 5 | **2.5 Twofish** | Key cố định, sinh IV ngẫu nhiên 16 byte | Giải mã chính xác. Save File và Open File khôi phục nguyên vẹn cặp `IV` và bản mã. | **ĐẠT (PASS)** |
| 6 | **2.6 AES-CTR** | Khóa 32 byte & Nonce 12 byte ngẫu nhiên (Base64) | Giải mã chuẩn xác, độ dài bản mã bằng đúng độ dài bản rõ (mã dòng không đệm). | **ĐẠT (PASS)** |
| 7 | **2.6 AES-CBC (Python)** | Văn bản dài tiếng Việt có dấu | Tự kiểm thử `SELF-TEST OK`, phát hiện sai khóa / sai IV ngay lập tức. | **ĐẠT (PASS)** |
