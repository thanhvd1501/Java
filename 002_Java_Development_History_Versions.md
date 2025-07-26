# Lịch sử phát triển và các phiên bản Java

## **Mục 1: Định nghĩa Cốt lõi**

Đây là quá trình tiến hóa của ngôn ngữ Java, từ khi ra đời cho đến nay. Nó bao gồm các phiên bản chính đã được phát hành, những tính năng quan trọng được thêm vào trong mỗi phiên bản, và sự thay đổi trong chiến lược phát hành.

### **Mục 2: Mục đích & Tầm quan trọng**

* **Mục đích:** Việc tìm hiểu lịch sử giúp chúng ta hiểu được **tại sao** Java lại có những đặc điểm như ngày nay. Nó giải thích sự ra đời của các tính năng, sự thay đổi trong cú pháp và triết lý thiết kế.
* **Tầm quan trọng:**
    1. **Chọn đúng phiên bản:** Biết về các phiên bản giúp bạn chọn đúng phiên bản Java cho dự án của mình (ví dụ: chọn phiên bản Hỗ trợ Dài hạn - LTS).
    2. **Sử dụng tính năng hiện đại:** Hiểu các phiên bản mới giúp bạn tận dụng được những tính năng mạnh mẽ, viết code ngắn gọn và hiệu quả hơn (ví dụ: Lambda, Stream API trong Java 8).
    3. **Bảo trì code cũ:** Khi làm việc với các dự án cũ, kiến thức về lịch sử giúp bạn hiểu tại sao code lại được viết theo một cách nhất định.

### **Mục 3: Cách hoạt động & Cơ chế bên trong**

Java đã trải qua nhiều giai đoạn phát triển:

1. **Dự án Green (1991):** Bắt đầu tại Sun Microsystems, ban đầu có tên là "Oak", nhằm mục đích tạo ra ngôn ngữ cho các thiết bị điện tử thông minh.
2. **Java 1.0 (1996):** Ra mắt chính thức, gây tiếng vang với triết lý "Viết một lần, chạy nhiều nơi".
3. **Kỷ nguyên Sun (1996 - 2010):** Java phát triển mạnh mẽ với các phiên bản quan trọng như J2SE 1.4 và đặc biệt là **Java 5 (2004)** với các tính năng đột phá (Generics, Enums, Autoboxing) và **Java 6 (2006)**.
4. **Kỷ nguyên Oracle (2010 - nay):** Oracle mua lại Sun Microsystems.
      * **Java 7 (2011):** Cải tiến nhỏ.
      * **Java 8 (2014):** Một cuộc cách mạng lần thứ hai. Giới thiệu **Lambda Expressions, Stream API, Optional**, thay đổi hoàn toàn cách viết code Java theo hướng lập trình hàm.
      * **Thay đổi chu kỳ phát hành (từ Java 9):** Oracle chuyển sang mô hình phát hành nhanh hơn, **6 tháng một phiên bản mới**. Đồng thời giới thiệu khái niệm **LTS (Long-Term Support)** - các phiên bản được hỗ trợ trong nhiều năm (như 8, 11, 17, 21).

### **Mục 4: Cú pháp & Ví dụ Code thực tế**

Lịch sử không có "cú pháp", nhưng chúng ta có thể xem sự thay đổi trong cách viết code qua các phiên bản.

**Ví dụ 1: Duyệt một danh sách (Trước Java 8)**

```java
// File: OldWay.java
import java.util.Arrays;
import java.util.List;

public class OldWay {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("An", "Binh", "Cuong");
        
        // Sử dụng vòng lặp for-each, một tính năng từ Java 5
        for (String name : names) {
            System.out.println(name);
        }
    }
}
```

**Ví dụ 2: Duyệt một danh sách (Với Lambda và Stream API của Java 8)**

```java
// File: NewWay.java
import java.util.Arrays;
import java.util.List;

public class NewWay {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("An", "Binh", "Cuong");

        // Sử dụng Stream API và method reference, ngắn gọn và tường minh hơn rất nhiều
        // "lấy danh sách tên, chuyển thành một dòng chảy, và với mỗi phần tử, in nó ra"
        names.stream().forEach(System.out::println);
    }
}
```

### **Mục 5: So sánh & Đối chiếu**

**So sánh các phiên bản LTS (Long-Term Support):**

| Phiên bản | Năm phát hành | Hỗ trợ mở rộng đến | Một số tính năng nổi bật                                 |
| :-------- | :------------ | :------------------ | :------------------------------------------------------ |
| **Java 8** | 2014          | \~2030               | Lambda, Stream API, Optional, Default Methods.          |
| **Java 11** | 2018          | \~2026               | Local-Variable Syntax for Lambda (`var`), HTTP Client mới. |
| **Java 17** | 2021          | \~2029               | Sealed Classes, Records, Pattern Matching for `instanceof`. |
| **Java 21** | 2023          | \~2031               | Virtual Threads, Sequenced Collections, Pattern Matching for `switch`. |

**So sánh mô hình phát hành:**

| Tiêu chí    | Mô hình cũ (Trước Java 9)                 | Mô hình mới (Sau Java 9)                                                                    |
| :---------- | :---------------------------------------- | :------------------------------------------------------------------------------------------ |
| **Tần suất** | Nhiều năm một phiên bản lớn (3-5 năm).     | 6 tháng một phiên bản mới.                                                                  |
| **Hỗ trợ** | Mỗi phiên bản được hỗ trợ trong thời gian dài. | Chỉ phiên bản LTS được hỗ trợ dài hạn. Các phiên bản trung gian chỉ được hỗ trợ đến khi phiên bản tiếp theo ra mắt. |

### **Mục 6: Lưu ý, "Best Practices" & Các lỗi thường gặp**

* **Lưu ý:** Không phải lúc nào phiên bản mới nhất cũng là tốt nhất cho mọi dự án. Các phiên bản không phải LTS có vòng đời rất ngắn.
* **Best Practices:**
  * Đối với các **dự án sản phẩm (production)**, hãy ưu tiên sử dụng phiên bản **LTS** gần nhất (hiện tại là 17 hoặc 21) để đảm bảo sự ổn định và nhận được các bản vá bảo mật.
  * Đối với việc **học tập hoặc các dự án cá nhân**, bạn có thể sử dụng phiên bản mới nhất để trải nghiệm các tính năng mới.
* **Lỗi thường gặp:** Sử dụng các phiên bản Java đã quá cũ và không còn được hỗ trợ (như Java 6, 7). Điều này khiến ứng dụng của bạn có nguy cơ đối mặt với các lỗ hổng bảo mật chưa được vá.
