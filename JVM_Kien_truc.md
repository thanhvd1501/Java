# 🏗 Kiến trúc và Cơ chế hoạt động của JVM

## 1. Giới thiệu tổng quan

Java Virtual Machine (JVM) là "trái tim" của nền tảng Java, hiện thực
hóa triết lý **"Write Once, Run Anywhere"**. Thay vì chạy trực tiếp trên
hệ điều hành, mã nguồn Java được biên dịch thành **bytecode**
(`.class`), và bytecode này chạy trên JVM. Nhờ vậy, cùng một chương
trình Java có thể chạy trên Windows, Linux hay macOS mà không cần biên
dịch lại.

------------------------------------------------------------------------

## 2. Class Loader Subsystem (Hệ thống nạp lớp)

Class Loader chịu trách nhiệm **nạp các file `.class` vào vùng nhớ JVM**
để chuẩn bị thực thi. Nó hoạt động theo 3 bước:

1.  **Loading (Nạp)**
    -   Nạp bytecode từ file `.class` vào bộ nhớ.\
    -   Các Class Loader con sẽ nạp lớp theo cơ chế phân cấp (Bootstrap,
        Extension, Application).  
        +  Bootstrap ClassLoader: Đây là classloader đầu tiên là siêu lớp của Extension classloader. Nó tải tệp rt.jar chứa tất cả các tệp lớp của Java Standard Edition như các lớp gói java.lang, java.net, java.util, java.io, java.sql v.v.  
        +  Extension ClassLoader: Đây là classloader con của Bootstrap và là classloader cha của System classloader. Được sử dụng để tải các tệp .jar chứa các lớp mở rộng của Java và các API của bên thứ ba.  
        +  System/Application ClassLoader: Đây là classloader con của Extension classloader. Nó tải các tệp lớp từ classpath – một biến môi trường hoặc một tùy chọn dòng lệnh cho phép người dùng chỉ định vị trí các tệp lớp được sử dụng trong ứng dụng Java.  
2.  **Linking (Liên kết)**
    -   **Verify (Xác minh):** kiểm tra bytecode có hợp lệ, an toàn hay
        không.\
    -   **Prepare (Chuẩn bị):** cấp phát bộ nhớ cho biến static, gán giá
        trị mặc định.\
    -   **Resolve (Phân giải):** thay thế các tham chiếu ký hiệu
        (symbolic reference) bằng tham chiếu thực (direct reference).
3.  **Initialization (Khởi tạo)**
    -   Gán giá trị thực cho biến static, chạy các khối static
        initializer.

👉 Kết quả: Sau khi Class Loader hoàn tất, lớp đã sẵn sàng để JVM sử
dụng.

------------------------------------------------------------------------

## 3. JVM Memory Areas (Các vùng nhớ do JVM quản lý)

Đây là phần quan trọng nhất, nơi JVM tổ chức bộ nhớ để thực thi chương
trình.

### a) Method Area (Class Area / Permanent Generation / Metaspace)

-   **Chức năng:** lưu metadata về lớp.\
-   **Nội dung:** thông tin cấu trúc class (tên lớp, tên phương thức,
    biến static, bảng hằng số runtime, bytecode của method).\
-   **Đặc điểm:** được **chia sẻ giữa các luồng** → giúp tiết kiệm bộ
    nhớ.

### b) Heap Area

-   **Chức năng:** lưu trữ **đối tượng và mảng**.\
-   **Đặc điểm:** vùng nhớ **dùng chung** cho tất cả luồng.\
-   **Vai trò đặc biệt:** Garbage Collector (GC) hoạt động tại đây để tự
    động giải phóng đối tượng không còn tham chiếu.

### c) Stack Area (JVM Stack)

-   **Chức năng:** mỗi luồng có một Stack riêng, lưu **Stack Frame** cho
    từng lời gọi phương thức.\
-   **Stack Frame chứa:**
    -   Local Variables (biến cục bộ).\
    -   Operand Stack (ngăn xếp toán hạng).\
    -   Reference đến Constant Pool của class.\
-   **Vòng đời:** một frame được tạo khi phương thức bắt đầu, và bị hủy
    khi kết thúc.

### d) PC Register (Thanh ghi bộ đếm chương trình)

-   Mỗi luồng có một **PC Register riêng**.\
-   Chức năng: lưu **địa chỉ lệnh bytecode JVM** hiện tại mà luồng đó
    đang thực thi.\
-   Giúp JVM hỗ trợ **đa luồng**, vì mỗi luồng cần biết mình đang chạy
    tới đâu.

### e) Native Method Stack

-   Khác với JVM Stack, ngăn xếp này dùng để thực thi **phương thức
    native** (viết bằng C/C++).\
-   Nó cung cấp môi trường cho các lời gọi hệ thống (system calls) hoặc
    thư viện native.

------------------------------------------------------------------------

## 4. Execution Engine (Bộ máy thực thi)

Đây là "bộ não" của JVM, chịu trách nhiệm **chuyển bytecode thành lệnh
máy** để CPU thực thi.

-   **Interpreter (Thông dịch viên):**
    -   Đọc và thực thi từng lệnh bytecode một cách tuần tự.\
    -   Ưu điểm: đơn giản, khởi động nhanh.\
    -   Nhược điểm: chậm khi chạy lâu dài vì lặp lại nhiều bytecode.
-   **JIT Compiler (Just-In-Time):**
    -   Khi một đoạn code được chạy nhiều lần, JIT biên dịch bytecode
        thành **mã máy gốc** để tăng tốc độ.\
    -   Ưu điểm: hiệu năng gần như native.\
    -   Nhược điểm: cần thời gian biên dịch ban đầu.
-   **Garbage Collector (GC):**
    -   Một phần của Execution Engine.\
    -   Tự động thu hồi bộ nhớ Heap, giải phóng đối tượng không còn tham
        chiếu.\
    -   Cơ chế: Mark and Sweep, Generational GC, G1 GC, v.v.

------------------------------------------------------------------------

## 5. Native Method Interface (JNI) & Java Native Libraries

-   **JNI (Java Native Interface):** cầu nối giữa Java và mã native.\
-   Cho phép:
    -   Java gọi các thư viện native (C, C++, Assembly).\
    -   Native code gọi ngược lại vào Java.\
-   **Java Native Libraries:** các thư viện hệ thống được liên kết thông
    qua JNI (như driver, API của OS).

------------------------------------------------------------------------

## 6. Tổng kết luồng hoạt động

1.  **Class Loader** nạp file `.class` vào Method Area.\
2.  **JVM Memory** cấp phát vùng nhớ cho đối tượng, phương thức,
    stack,...\
3.  **Execution Engine** đọc bytecode và thực thi:
    -   Interpreter thực thi tuần tự.\
    -   JIT Compiler biên dịch thành mã máy tối ưu.\
    -   GC dọn dẹp đối tượng không dùng.\
4.  Khi cần, chương trình Java có thể gọi thư viện native qua **JNI** →
    tương tác với hệ điều hành hoặc phần cứng.

👉 Nhờ kiến trúc này, Java duy trì được tính **đa nền tảng, bảo mật và
tối ưu hiệu năng**, đúng với triết lý **"Write Once, Run Anywhere"**.
