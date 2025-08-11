
# Java Nền Tảng – Tài Liệu Chuyên Sâu

## 1. Tổng quan
**Mục tiêu học:**
- Nắm vững nền tảng Java về quản lý bộ nhớ, kiểu dữ liệu, từ khóa, quy tắc đặt tên, toán tử, và sự khác biệt primitive vs wrapper.
- Biết áp dụng trong code sản xuất với hiệu năng và độ an toàn cao.
- Tránh bẫy logic (null, boxing overhead, overflow, so sánh sai).

**Bức tranh tổng thể:**
- **GC** đảm bảo quản lý bộ nhớ tự động.
- **Keywords** là “từ khóa đặc biệt” của ngôn ngữ.
- **Naming** tăng tính đọc hiểu và duy trì.
- **Primitive & Wrapper** ảnh hưởng trực tiếp đến hiệu năng và độ an toàn.
- **Operators** là nền tảng xử lý logic và số học.

**Ứng dụng thực tế:**
- Viết API, service logic, batch job, xử lý dữ liệu số lượng lớn.
- Code review, mentoring junior, chuẩn hoá coding style trong team.

---

## 2. Giải thích chi tiết

### 2.1 Garbage Collection (GC)
**Định nghĩa:**
GC là cơ chế tự động thu hồi bộ nhớ các đối tượng không còn được tham chiếu.

**Cơ chế làm việc:**
- **Heap Memory** chia thành:
  - **Young Generation** (Eden + Survivor): nơi object mới tạo.
  - **Old Generation**: nơi object sống lâu.
  - (Metaspace: lưu metadata class).
- **Minor GC**: dọn Young Gen → nhanh.
- **Major GC**: dọn Old Gen → chậm hơn.
- **Stop-the-World**: JVM tạm dừng tất cả thread ứng dụng khi GC.
- **Safepoint**: điểm JVM đảm bảo an toàn khi dừng thread.
- **Thuật toán**: Mark–Sweep–Compact, G1 (mặc định JDK17), ZGC, Shenandoah.

**Khi nào chạy:**
- Khi Young đầy → Minor GC.
- Khi Old đầy hoặc threshold → Major GC.
- Có thể trigger thủ công `System.gc()` (không khuyến khích).

**GC Logs cơ bản:**
```bash
java -XX:+PrintGCDetails -Xmx256m -Xms256m Main
```

**Ví dụ code:**
```java
List<byte[]> list = new ArrayList<>();
while (true) {
    list.add(new byte[1024 * 1024]); // 1MB
}
```
*Khi chạy sẽ thấy GC log do heap đầy.*

**Bẫy:**
- Lạm dụng tạo object mới.
- Giữ reference không cần thiết (memory leak logic-level).

**Best practices:**
- Dùng pool (thread, connection) thay vì tạo mới liên tục.
- Tránh giữ object lâu hơn cần thiết.

---

### 2.2 Keywords trong Java
**Nhóm & ví dụ:**

| Nhóm             | Keyword                  | Ví dụ |
|------------------|--------------------------|-------|
| Điều khiển luồng | if, else, switch, for, while, do, break, continue, return | `if (x>0) return;` |
| Khai báo         | class, interface, enum, record, var (Java10+) | `public class MyClass {}` |
| Truy cập         | public, private, protected, default | `private int age;` |
| Đồng bộ          | synchronized, volatile | `synchronized void m(){}` |
| Xử lý lỗi        | try, catch, finally, throw, throws | `try {...} catch(Exception e){}` |

**Bẫy:**
- Nhầm `default` trong access modifier và trong `switch`.

---

### 2.3 Naming Conventions
**Quy tắc chuẩn (Oracle/Google):**
- **Class/Interface**: PascalCase → `OrderService`
- **Object/variable/method**: camelCase → `orderService`, `calculateTotal()`
- **Constant**: UPPER_CASE_WITH_UNDERSCORES → `MAX_RETRY`
- **Package**: all lowercase, domain reverse → `com.example.util`

**Ví dụ sai:**
```java
public class order_service {} // Sai: class phải PascalCase
```

**Best practices:**
- Không viết tắt khó hiểu (`int cnt` → nên là `count`).
- Tên phải phản ánh ý nghĩa, tránh “magic number”.

---

### 2.4 Primitive Data Types
| Kiểu | Bit | Phạm vi | Default | Ghi chú |
|------|-----|---------|---------|---------|
| byte | 8   | -128 ~ 127 | 0 | tiết kiệm bộ nhớ |
| short| 16  | -32768 ~ 32767 | 0 | ít dùng |
| int  | 32  | ~2.1e9 | 0 | phổ biến nhất |
| long | 64  | ~9e18 | 0L | dùng cho số lớn |
| float| 32  | ~7 chữ số thập phân | 0.0f | mất chính xác |
| double| 64 | ~15 chữ số thập phân | 0.0d | mặc định cho số thực |
| char | 16  | '\u0000'~'\uffff' | '\u0000' | UTF-16 |
| boolean| 1 (JVM-dependent) | true/false | false | lưu như byte/bit |

---

### 2.5 Primitive vs Object (Wrapper)
**So sánh:**
- Primitive: nhanh, ít tốn bộ nhớ, không null.
- Wrapper: chậm hơn, tốn bộ nhớ, có thể null, dùng trong collections.

**Bẫy:**
- Auto-boxing gây overhead.
- NullPointer khi unbox null.

---

### 2.6 int vs Integer
- `int`: primitive, so sánh bằng `==` là giá trị.
- `Integer`: object, `==` so sánh reference, `.equals()` so sánh giá trị.
- Cache `Integer` từ -128 đến 127.

```java
Integer a = 127, b = 127; // a==b true
Integer x = 128, y = 128; // x==y false
```

---

### 2.7 Autoboxing & Unboxing
- Autoboxing: primitive → wrapper (`int` → `Integer.valueOf()`).
- Unboxing: wrapper → primitive (`Integer.intValue()`).
- Bẫy NPE khi unbox null.

---

### 2.8 Thực hành Autoboxing/Unboxing
Bài 1:
```java
Integer x = null;
int y = x; // NPE
```

---

### 2.9 Toán tử gán & so sánh
- `=`: gán.
- `+=`: gán cộng.
- `==`: so sánh giá trị với primitive, reference với object.
- `.equals()`: so sánh giá trị object.

---

### 2.10 Thực hành gán & so sánh
Bài:
```java
Integer a = 1000, b = 1000;
System.out.println(a==b); // false
```

---

### 2.11 Toán tử toán học
- `+ - * / % ++ --`
- Chia nguyên: bỏ phần thập phân.
- Overflow: quay vòng giá trị.

```java
int x = Integer.MAX_VALUE;
System.out.println(x+1); // overflow → -2147483648
```

---

## 8. Tài liệu tham khảo
- [Oracle Java SE Documentation](https://docs.oracle.com/en/java/javase/17/)
- Effective Java – Joshua Bloch
- [Java Language Specification](https://docs.oracle.com/javase/specs/)
