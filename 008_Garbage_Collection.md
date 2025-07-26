# Khái niệm Garbage Collection (GC - Thu gom rác)

### **Mục 1: Định nghĩa Cốt lõi**

**Garbage Collection (GC)** là một tiến trình **hoàn toàn tự động** của JVM, có chức năng như một người dọn dẹp bộ nhớ. Nhiệm vụ của nó là tìm kiếm và thu hồi những vùng nhớ đang bị chiếm giữ bởi các đối tượng mà chương trình không còn cách nào sử dụng được nữa (gọi là "rác" - garbage).

* **Ví von đời thực:** 🗑️ Hãy tưởng tượng bộ nhớ Heap của bạn là một phòng làm việc. Mỗi khi bạn cần một tờ giấy ghi chú mới, bạn lấy một tờ từ xấp giấy (lệnh `new Object()`). Bạn viết, sử dụng nó, rồi có thể bạn vò nát và ném nó xuống sàn khi không cần nữa (đối tượng mất tham chiếu). Nếu không ai dọn, căn phòng sẽ ngập trong giấy vụn. **Garbage Collector** chính là người lao công cần mẫn, định kỳ đi vào phòng, nhặt hết những mẩu giấy bị vứt đi (rác) và cho vào thùng, trả lại không gian sạch sẽ cho bạn làm việc.

### **Mục 2: Mục đích & Tầm quan trọng**

* **Mục đích:** Giải phóng lập trình viên khỏi gánh nặng phải quản lý bộ nhớ thủ công.
* **Tầm quan trọng:** GC là một trong những tính năng "đắt giá" nhất của Java, mang lại hai lợi ích to lớn:

1. **Tăng độ tin cậy và giảm lỗi:** Trong C/C++, việc quên giải phóng bộ nhớ (`memory leak`) hoặc giải phóng bộ nhớ hai lần là những lỗi cực kỳ nguy hiểm và khó tìm. GC đã loại bỏ gần như hoàn toàn lớp lỗi này.
2. **Tăng năng suất lập trình:** Bạn có thể tập trung vào việc giải quyết bài toán kinh doanh thay vì phải bận tâm đến việc cấp phát và thu hồi từng byte bộ nhớ.

### **Mục 3: Cách hoạt động & Cơ chế bên trong**

Nguyên tắc vàng của GC là **khả năng truy cập (Reachability)**. Một đối tượng được coi là "còn sống" nếu có một đường dẫn tham chiếu từ "GC Roots" (gốc rễ) đến nó. GC Roots là các tham chiếu luôn sống, ví dụ như các biến trên Stack của một luồng đang chạy, hoặc các biến `static`.

Cơ chế phổ biến nhất là **Mark-and-Sweep (Đánh dấu và Dọn dẹp)**:

1. **Giai đoạn Mark (Đánh dấu):** GC tạm dừng ứng dụng một chút. Nó bắt đầu từ các GC Roots và đi theo mọi liên kết tham chiếu. Bất kỳ đối tượng nào nó chạm tới sẽ được "đánh dấu" là còn sống.
2. **Giai đoạn Sweep (Dọn dẹp):** Sau khi đánh dấu xong, GC quét toàn bộ Heap. Những đối tượng nào **không được đánh dấu** sẽ bị coi là rác, và vùng nhớ của chúng sẽ được thu hồi để tái sử dụng.

Để tăng hiệu quả, các JVM hiện đại sử dụng **Generational GC (GC theo thế hệ)**. Heap được chia thành:

* **Young Generation (Thế hệ trẻ):** Nơi hầu hết các đối tượng mới được sinh ra. Vùng này được dọn dẹp rất thường xuyên (*Minor GC*). Giả thuyết là hầu hết các đối tượng đều có vòng đời rất ngắn.
* **Old Generation (Thế hệ già):** Những đối tượng sống sót qua nhiều vòng dọn dẹp ở vùng Young sẽ được "thăng chức" lên đây. Vùng này ít được dọn dẹp hơn (*Major GC*), vì các đối tượng ở đây được coi là sẽ sống lâu.

### **Mục 4: Cú pháp & Ví dụ Code thực tế**

Bạn không có cú pháp để ra lệnh cho GC, nhưng bạn có thể viết code tạo ra "rác" để nó dọn.

**Ví dụ 1: Tạo ra rác**

```java
// File: MakeGarbage.java
public class MakeGarbage {

public static void main(String[] args) {
// Một đối tượng String "Tôi là rác" được tạo ra.
// Nó được gán cho biến 'message'. Hiện tại nó "còn sống".
String message = new String("Tôi là rác");

// Sau dòng này, biến 'message' không còn trỏ đến đối tượng String kia nữa.
// Đối tượng String "Tôi là rác" không còn ai tham chiếu đến.
// Nó đã chính thức trở thành "rác" và sẽ bị GC thu dọn trong tương lai.
message = "Một thông điệp khác";
}
}
```

**Ví dụ 2: Memory Leak - Trường hợp GC bó tay**

```java
// File: MemoryLeakExample.java
import java.util.ArrayList;
import java.util.List;

public class MemoryLeakExample {
// Một danh sách tĩnh, nó sẽ tồn tại suốt vòng đời của ứng dụng.
public static final List<byte[]> leakyList = new ArrayList<>();

public void createLeakyObject() {
// Phương thức này tạo ra một đối tượng lớn (10MB)
// và thêm nó vào danh sách tĩnh.
leakyList.add(new byte[10 * 1024 * 1024]);
}

public static void main(String[] args) {
MemoryLeakExample example = new MemoryLeakExample();
while (true) {
// Liên tục gọi phương thức tạo đối tượng và thêm vào list.
example.createLeakyObject();
System.out.println("Heap đang bị rò rỉ...");
// Mặc dù chúng ta không bao giờ dùng lại các đối tượng byte[] này,
// nhưng vì chúng vẫn được 'leakyList' giữ tham chiếu,
// GC sẽ không thể dọn dẹp chúng -> gây ra OutOfMemoryError.
}
}
}
```

Đây là một ví dụ điển hình về rò rỉ bộ nhớ. Lỗi không nằm ở GC mà ở logic của lập trình viên.

### **Mục 5: So sánh & Đối chiếu**

| Tiêu chí       | Quản lý bộ nhớ thủ công (C++)                                    | Quản lý bộ nhớ tự động (Java GC)                                     |
| :-------------- | :--------------------------------------------------------------- | :------------------------------------------------------------------- |
| **Trách nhiệm** | **Lập trình viên** phải gọi `delete` / `free`.                    | **JVM** tự động quản lý.                                             |
| **Ưu điểm** | Kiểm soát tuyệt đối, tiềm năng hiệu năng cao.                    | An toàn, đơn giản, năng suất cao.                                     |
| **Nhược điểm** | Phức tạp, dễ gây lỗi nghiêm trọng (memory leak).                 | Có thể có những khoảng dừng nhỏ (GC pause).                            |

### **Mục 6: Lưu ý, "Best Practices" & Các lỗi thường gặp**

* **Lưu ý:** GC chỉ giải phóng **bộ nhớ**. Nó không tự động đóng các tài nguyên khác như kết nối cơ sở dữ liệu, file, hay socket. Bạn phải tự quản lý chúng.
* **Best Practices:**
* **Đừng gọi `System.gc()`**. Đây là một thói quen xấu. Hãy để JVM tự quyết định thời điểm tốt nhất để dọn dẹp. Việc ép buộc GC có thể gây ra một đợt dọn dẹp toàn bộ (Full GC) không cần thiết, làm ứng dụng bị "khựng" lại.
* Để đóng các tài nguyên ngoài bộ nhớ một cách an toàn, hãy luôn sử dụng khối lệnh **`try-with-resources`**, được giới thiệu từ Java 7.
* Hãy cố gắng giới hạn phạm vi (scope) của các biến. Khi một biến ra khỏi scope, đối tượng nó trỏ tới sẽ có cơ hội trở thành rác.
* **Lỗi thường gặp:**
* **Memory Leak (Rò rỉ bộ nhớ):** Như ví dụ trên, lỗi phổ biến nhất là giữ lại các tham chiếu đến những đối tượng không còn cần thiết, đặc biệt là trong các `static collection`.
* **Dựa vào `finalize()`:** Lầm tưởng `finalize()` là một phương thức hủy (destructor) và dùng nó để giải phóng tài nguyên. `finalize()` không được đảm bảo sẽ chạy và đã bị loại bỏ (deprecated) trong các phiên bản Java hiện đại. **Tuyệt đối không sử dụng nó.**
