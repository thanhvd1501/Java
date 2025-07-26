# Quy tắc đặt tên (Naming Convention)

## **Mục 1: Định nghĩa Cốt lõi**

**Quy tắc đặt tên (Naming Convention)** là một bộ các hướng dẫn về cách đặt tên cho các thành phần trong code (lớp, biến, phương thức,...). Những quy tắc này **không bị ép buộc** bởi trình biên dịch, nhưng được cộng đồng lập trình viên Java trên toàn thế giới tuân theo một cách nghiêm ngặt để tạo ra mã nguồn **thống nhất, dễ đọc và dễ bảo trì**.

* **Ví von đời thực:** 📜 Quy tắc đặt tên cũng giống như quy tắc trình bày một bài báo khoa học. Bạn có thể viết một bài báo mà không cần tiêu đề, đề mục, hay chú thích. Nhưng một bài báo được trình bày đúng chuẩn sẽ giúp người đọc nắm bắt thông tin nhanh và chính xác hơn rất nhiều. Code cũng vậy, sự rõ ràng và nhất quán là vua.

### **Mục 2: Mục đích & Tầm quan trọng**

* **Mục đích:** Tạo ra một "ngôn ngữ chung" về mặt hình thức cho các lập trình viên, giúp code trở nên dễ đoán và nhất quán.
* **Tầm quan trọng:**
* **Tăng khả năng đọc hiểu (Readability):** Một cái tên rõ ràng như `calculateSalesTax` nói lên nhiều điều hơn là `calcTax` hay `cst`. Code được đọc nhiều hơn được viết, vì vậy hãy tối ưu cho người đọc.
* **Tự tài liệu hóa (Self-documenting):** Nhìn vào tên biến `isUserAuthenticated`, bạn không cần đọc thêm chú thích cũng biết nó là một biến `boolean` và mục đích của nó là gì.
* **Tăng tốc độ làm việc nhóm:** Khi mọi người trong đội đều tuân theo một quy tắc chung, việc đọc, sửa và tích hợp code của nhau trở nên dễ dàng hơn rất nhiều.

### **Mục 3: Cách hoạt động & Cơ chế bên trong**

Đây là một quy ước mang tính xã hội, không phải cơ chế kỹ thuật. Trình biên dịch Java hoàn toàn chấp nhận `class my_class { int MY_VARIABLE; }`, nhưng các lập trình viên khác sẽ thấy rất khó chịu khi đọc nó.

Tuy nhiên, trong các dự án chuyên nghiệp, các công cụ phân tích mã tĩnh (static analysis tools) như **Checkstyle** hoặc **SonarQube** thường được tích hợp vào quy trình build. Các công cụ này sẽ tự động quét code và báo lỗi nếu phát hiện các vi phạm về quy tắc đặt tên, đảm bảo rằng mọi người đều tuân thủ.

### **Mục 4: Cú pháp & Ví dụ Code thực tế**

Bảng tổng hợp các quy tắc quan trọng nhất:

| Loại                  | Quy tắc                           | Ví dụ                                          |
| :-------------------- | :-------------------------------- | :--------------------------------------------- |
| **Lớp (Class), Enum** | **PascalCase** (hay UpperCamelCase). Là danh từ. | `UserService`, `HttpRequest`, `OrderStatus`    |
| **Giao diện (Interface)** | **PascalCase**. Là danh từ hoặc tính từ. | `Runnable`, `Comparable`, `DataRepository`     |
| **Phương thức (Method)** | **camelCase**. Bắt đầu bằng động từ. | `getUserById()`, `calculateTotal()`, `save()`    |
| **Biến (Variable)** | **camelCase**. Ngắn gọn nhưng mang đầy đủ ý nghĩa. | `firstName`, `remainingAttempts`, `connection` |
| **Hằng số (Constant)** | **UPPER\_CASE\_SNAKE\_CASE**.        | `MAXIMUM_POOL_SIZE`, `INTEREST_RATE`           |
| **Gói (Package)** | **lowercase**. Theo tên miền đảo ngược. | `com.google.gson`, `org.springframework.boot` |

**Ví dụ một file Java tuân thủ đầy đủ quy tắc:**

```java
// package: com.mycompany.ecommerce
package com.mycompany.ecommerce;

import java.util.List;

// interface: PascalCase
interface Searchable {
// method: camelCase
List<Product> searchByName(String query);
}

// class: PascalCase
public class ProductService implements Searchable {

// constant: UPPER_CASE_SNAKE_CASE
public static final int DEFAULT_PAGE_SIZE = 10;

// variable: camelCase
private String databaseUrl;
private int connectionTimeout;

// method (getter): camelCase
public String getDatabaseUrl() {
return this.databaseUrl;
}

// method (setter): camelCase
public void setConnectionTimeout(int timeout) {
this.connectionTimeout = timeout;
}

@Override
public List<Product> searchByName(String query) {
// ... implementation code ...
return null;
}
}
```

### **Mục 5: So sánh & Đối chiếu**

* **camelCase vs. PascalCase vs. snake\_case**
* **`camelCase`**: Từ đầu tiên viết thường, các từ sau viết hoa chữ cái đầu. Dùng cho **biến** và **phương thức**.
* **`PascalCase`**: Mọi từ đều viết hoa chữ cái đầu. Dùng cho **lớp** và **giao diện**.
* **`snake_case`**: Các từ viết thường nối với nhau bằng `_`. **Không dùng trong Java** (ngoại trừ biến thể `UPPER_CASE_SNAKE_CASE` cho hằng số), nhưng là quy ước chính trong các ngôn ngữ như Python, Ruby, C.

### **Mục 6: Lưu ý, "Best Practices" & Các lỗi thường gặp**

* **Lưu ý:** Không nên bắt đầu tên biến bằng `_` hoặc `$`. Mặc dù hợp lệ, `$` được dành riêng cho các lớp do trình biên dịch tạo ra.
* **Best Practices:**
* **QUAN TRỌNG NHẤT: Đặt tên có ý nghĩa\!** Tên phải mô tả rõ ràng mục đích của biến/phương thức/lớp đó. `customerAddress` tốt hơn ngàn lần `addr` hay `str1`.
* Biến `boolean` nên có tiền tố `is`, `has`, `can` (ví dụ: `isAvailable`, `hasPermission`).
* Tên phương thức nên là động từ hoặc cụm động từ thể hiện hành động.
* Tránh các tên viết tắt khó hiểu.
* **Lỗi thường gặp:**
* Đặt tên lớp bằng camelCase (`class productRepository`).
* Đặt tên biến bằng PascalCase (`String CustomerName`).
* Đặt tên phương thức bằng PascalCase (`void SendEmail()`).
* Dùng tên một chữ cái (`a`, `b`, `c`) cho các biến quan trọng, thay vì chỉ dùng cho biến đếm trong các vòng lặp ngắn (`i`, `j`, `k`).