# Các từ khóa (Keywords) trong Java

## **Mục 1: Định nghĩa Cốt lõi**

**Keywords (Từ khóa)** là những từ được **dành riêng** (reserved words) trong ngôn ngữ Java. Chúng có ý nghĩa đặc biệt đối với trình biên dịch và không thể được sử dụng cho bất kỳ mục đích nào khác, chẳng hạn như đặt tên cho biến, phương thức, hoặc lớp.

* **Ví von đời thực:** 🔑 Hãy coi các từ khóa như những ký hiệu trên bàn cờ vua. Con "Vua", "Hậu", "Tượng" đều có những cái tên và cách đi được quy định sẵn. Bạn không thể gọi một con Tốt là "Vua" và mong nó có thể đi như Vua được. Tương tự, trong Java, `class`, `public`, `if`, `while` là những từ có "luật chơi" cố định mà trình biên dịch đã quy định.

### **Mục 2: Mục đích & Tầm quan trọng**

* **Mục đích:** Các từ khóa tạo nên bộ khung xương, cấu trúc và ngữ pháp cho ngôn ngữ Java. Chúng định nghĩa mọi thứ, từ kiểu dữ liệu (`int`, `double`), luồng điều khiển (`if`, `for`), mức độ truy cập (`public`, `private`) cho đến các khái niệm của lập trình hướng đối tượng (`class`, `interface`, `new`).
* **Tầm quan trọng:** Việc nắm vững các từ khóa là **yêu cầu cơ bản và tuyệt đối** để có thể viết code Java. Nếu không hiểu ý nghĩa của chúng, bạn không thể "giao tiếp" được với trình biên dịch. Hiểu chúng giúp bạn đọc code của người khác một cách dễ dàng và thể hiện logic của mình một cách chính xác.

### **Mục 3: Cách hoạt động & Cơ chế bên trong**

Khi bạn biên dịch một file Java (`javac MyCode.java`), trình biên dịch sẽ thực hiện một quá trình gọi là "phân tích từ vựng" (lexical analysis).

1. Nó đọc mã nguồn của bạn và bẻ gãy thành các đơn vị nhỏ nhất có ý nghĩa, gọi là "tokens".
2. Trình biên dịch có một danh sách nội bộ chứa tất cả các từ khóa của Java.
3. Với mỗi token, nó sẽ kiểm tra xem token đó có khớp với từ nào trong danh sách từ khóa không.
4. Nếu bạn cố gắng dùng một từ khóa làm tên biến, ví dụ `int static = 10;`, trình biên dịch sẽ ngay lập tức nhận ra `static` là một từ khóa và báo lỗi biên dịch ngay, vì nó mong đợi một tên định danh (identifier) hợp lệ chứ không phải một từ khóa.

### **Mục 4: Cú pháp & Ví dụ Code thực tế**

Cú pháp chính là việc sử dụng các từ khóa này một cách chính xác trong các câu lệnh Java.

#### Ví dụ 1: Các từ khóa cơ bản và phổ biến

```java
// 'package': định nghĩa gói chứa lớp này.
package com.example;

// 'public': bộ định nghĩa truy cập, ai cũng thấy.
// 'class': định nghĩa một lớp.
public class KeywordShowcase {

// 'private': chỉ có thể truy cập bên trong lớp này.
// 'static': thuộc về lớp, không phải đối tượng.
// 'final': giá trị là hằng số, không thể thay đổi.
private static final double PI_APPROXIMATION = 3.14;

// 'protected': có thể truy cập bởi các lớp trong cùng gói và lớp con.
protected String objectName;

// 'void': phương thức này không trả về giá trị.
public void printInfo() {
// 'int': kiểu dữ liệu số nguyên.
int counter = 0;

// 'while': vòng lặp, chạy khi điều kiện còn đúng.
while (counter < 5) {
// 'if', 'else': rẽ nhánh luồng điều khiển.
if (counter % 2 == 0) {
System.out.println("Số chẵn");
} else {
System.out.println("Số lẻ");
}
// 'continue': bỏ qua phần còn lại của vòng lặp và đi đến lần lặp tiếp theo.
if (counter == 3) {
counter++;
continue; 
}
counter++;
}
}

// 'boolean': kiểu dữ liệu đúng/sai.
// 'return': trả về một giá trị từ phương thức.
public boolean isReady() {
// 'true': một giá trị chữ (literal) dành riêng.
return true;
}
}
```

**Ví dụ 2: Các từ khóa nâng cao (`synchronized`, `volatile`, `assert`)**

```java
// 'abstract': lớp này không thể tạo đối tượng trực tiếp, phải được kế thừa.
public abstract class AdvancedConcepts {

// 'volatile': đảm bảo các thay đổi của biến này được các luồng khác thấy ngay lập tức.
private volatile boolean shutdownRequested = false;

// 'synchronized': đảm bảo tại một thời điểm chỉ có một luồng được chạy phương thức này.
public synchronized void performThreadSafeOperation() {
// ... code cần sự an toàn trong môi trường đa luồng
}

public void checkValue(int value) {
// 'assert': kiểm tra một giả định. Nếu giả định sai, nó sẽ ném ra AssertionError.
// Thường dùng để gỡ lỗi, cần bật bằng cờ -ea khi chạy.
assert value > 0 : "Giá trị phải là số dương";

// ... xử lý giá trị
}
}
```

### **Mục 5: So sánh & Đối chiếu**

* **Từ khóa (Keywords) vs. Từ chữ dành riêng (Reserved Literals)**
* **Keywords:** Là các từ lệnh (`if`, `class`, `void`).
* **Reserved Literals:** Là các giá trị cố định (`true`, `false`, `null`). Về mặt kỹ thuật chúng không phải là từ khóa nhưng bạn cũng không thể dùng chúng làm tên biến.
* **`const` và `goto`**
* Đây là hai từ khóa đặc biệt: chúng được Java **dành riêng nhưng không sử dụng**. Bạn không thể dùng chúng làm tên, nhưng chúng cũng chẳng có chức năng gì. Việc này nhằm tránh nhầm lẫn cho các lập trình viên từ C++ chuyển sang và để ngỏ cho tương lai.

### **Mục 6: Lưu ý, "Best Practices" & Các lỗi thường gặp**

* **Lưu ý:** Danh sách từ khóa có thể được bổ sung trong các phiên bản Java mới, dù rất hiếm. Ví dụ `var`, `record`, `sealed` là các từ khóa mới được thêm vào trong những năm gần đây.
* **Best Practices:**
* Đừng cố học thuộc lòng tất cả các từ khóa một lúc. Hãy học chúng theo từng nhóm chức năng (ví dụ: nhóm luồng điều khiển, nhóm kiểm soát truy cập).
* Tận dụng các trình soạn thảo code (IDE) hiện đại. Chúng sẽ tự động tô màu các từ khóa, giúp bạn nhận biết chúng một cách trực quan.
* **Lỗi thường gặp:**
* Lỗi sơ đẳng và phổ biến nhất là cố gắng đặt tên biến, phương thức hoặc lớp trùng với một từ khóa.
* `String switch = "light";` // Lỗi\! `switch` là từ khóa.
* `boolean continue = false;` // Lỗi\! `continue` là từ khóa
