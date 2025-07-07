# Ứng dụng Chat Bảo mật với Mã hóa Lai

Đây là một ứng dụng chat web thời gian thực, được xây dựng với Python (Flask, Flask-Sock) cho backend và JavaScript cho frontend. Tính năng cốt lõi của ứng dụng là mô hình mã hóa đầu cuối, sử dụng phương pháp lai kết hợp giữa RSA và DES để đảm bảo tính bí mật, toàn vẹn và xác thực của tin nhắn.

## Tính năng nổi bật

-   **Xác thực người dùng**: Hệ thống đăng ký và đăng nhập người dùng an toàn.
-   **Giao tiếp thời gian thực**: Chức năng chat trực tiếp được hỗ trợ bởi WebSocket.
-   **Danh sách người dùng**: Xem danh sách những người dùng khác đã đăng ký.
-   **Mã hóa đầu cuối**: Một cơ chế mã hóa lai mạnh mẽ đảm bảo chỉ những người dùng trong cuộc trò chuyện mới có thể đọc được tin nhắn.
-   **Cơ sở dữ liệu**: Sử dụng SQLite với SQLAlchemy ORM để lưu trữ dữ liệu lâu dài.
-   **Giao diện hiện đại**: Giao diện người dùng sạch sẽ, hiện đại cho các trang đăng nhập, đăng ký và trò chuyện.

***

## Giải thích Cơ chế Bảo mật

Ứng dụng triển khai một chiến lược **mã hóa lai** để bảo mật thông tin liên lạc. Cách tiếp cận này tận dụng thế mạnh của cả mã hóa bất đối xứng (RSA) và mã hóa đối xứng (DES).

1.  **Đăng ký & Tạo khóa**:
    * Khi một người dùng mới đăng ký, máy chủ sẽ tạo một **cặp khóa RSA 2048-bit** (khóa công khai và khóa riêng tư) duy nhất cho họ.
    * Các khóa này được lưu trữ an toàn trong cơ sở dữ liệu và liên kết với tài khoản của người dùng.

2.  **Bắt tay & Trao đổi khóa đối xứng**:
    * Khi Người dùng A muốn bắt đầu trò chuyện với Người dùng B, một quy trình "bắt tay" (handshake) an toàn sẽ diễn ra.
    * Client của Người dùng A lấy **khóa RSA công khai** của Người dùng B từ máy chủ.
    * Client của Người dùng A sau đó tạo một **khóa DES đối xứng** ngẫu nhiên và tạm thời.
    * Khóa DES này được mã hóa bằng khóa RSA công khai của Người dùng B. Điều này đảm bảo chỉ Người dùng B (người giữ khóa riêng tư tương ứng) mới có thể giải mã nó.
    * Người dùng A gửi khóa DES đã được mã hóa này cho Người dùng B.

3.  **Truyền tin nhắn an toàn**:
    * Người dùng B nhận được tin nhắn và sử dụng **khóa RSA riêng tư** của mình để giải mã khóa DES tạm thời.
    * Bây giờ, cả Người dùng A và Người dùng B đều có chung một khóa DES bí mật cho phiên trò chuyện hiện tại.
    * Tất cả các tin nhắn trò chuyện sau đó giữa họ sẽ được mã hóa và giải mã bằng **khóa DES đối xứng** này, vốn rất nhanh và hiệu quả.
    * Để đảm bảo tính toàn vẹn của tin nhắn và tính xác thực của người gửi, mỗi tin nhắn được mã hóa cũng được băm (sử dụng **SHA-256**) và sau đó được ký bằng khóa RSA riêng tư của người gửi. Người nhận sẽ xác minh chữ ký này bằng khóa công khai của người gửi.

Mô hình lai này vừa cung cấp tính bảo mật cao của mã hóa bất đối xứng cho việc trao đổi khóa, vừa có được hiệu suất cao của mã hóa đối xứng cho cuộc trò chuyện.

***

## Công nghệ sử dụng

-   **Backend**: Python, Flask, Flask-Sock, SQLAlchemy
-   **Mã hóa**: `pycryptodome` (cho RSA, DES, SHA-256), `werkzeug.security` (để băm mật khẩu)
-   **Frontend**: HTML5, CSS3, JavaScript
-   **Thư viện Crypto Frontend**: `JSEncrypt` (cho RSA), `CryptoJS` (cho DES, SHA-256)
-   **Cơ sở dữ liệu**: SQLite
-   **Giao thức**: WebSockets

***

## Hướng dẫn Cài đặt và Chạy ứng dụng

Làm theo các bước sau để chạy ứng dụng trên máy cục bộ của bạn.

1.  **Clone Repository**
    ```bash
    git clone <your-repository-url>
    cd <repository-directory>
    ```

2.  **Tạo và Kích hoạt Môi trường ảo**
    ```bash
    # Đối với Windows
    python -m venv venv
    .\venv\Scripts\activate

    # Đối với macOS/Linux
    python3 -m venv venv
    source venv/bin/activate
    ```

3.  **Cài đặt các gói phụ thuộc**
    Sử dụng tệp `requirements.txt` được cung cấp để cài đặt tất cả các gói Python cần thiết.
    ```bash
    pip install -r requirements.txt
    ```

4.  **Chạy ứng dụng**
    ```bash
    python server.py
    ```
    Máy chủ Flask sẽ khởi động, thường ở địa chỉ `http://127.0.0.1:5000`.

5.  **Truy cập ứng dụng**
    * Mở trình duyệt web của bạn và truy cập `http://127.0.0.1:5000`.
    * Bạn sẽ được chuyển hướng đến trang đăng nhập.
    * Hãy đăng ký hai hoặc nhiều người dùng khác nhau để kiểm tra chức năng trò chuyện. Tệp `database.db` sẽ được tự động tạo ra khi người dùng đầu tiên đăng ký.

***

## Cấu trúc tệp

```
.
├── server.py           # Ứng dụng Flask chính, xử lý WebSocket, logic mã hóa phía backend
├── models.py           # Model cơ sở dữ liệu SQLAlchemy, logic đăng ký và tạo khóa
├── requirements.txt    # Các gói Python phụ thuộc
├── templates/
│   ├── login.html      # Giao diện đăng nhập
│   ├── register.html   # Giao diện đăng ký
│   └── chat.html       # Giao diện chat chính với toàn bộ logic frontend
└── README.md           # Tệp này
```