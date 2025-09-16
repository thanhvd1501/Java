
### 1. Tổng quan

**Mục tiêu học:** Buổi học này nhằm mục đích xây dựng một nền tảng Java vững chắc, không chỉ dừng lại ở việc "viết code chạy được" mà là "viết code đúng, sạch, hiệu quả và dễ bảo trì". Các khái niệm này là viên gạch đầu tiên cho mọi chương trình Java, từ ứng dụng console đơn giản đến hệ thống enterprise phức tạp.

**Bức tranh tổng thể:** Chúng ta sẽ khám phá cách Java quản lý bộ nhớ tự động (GC), "bảng chữ cái" của ngôn ngữ (keywords), quy tắc giao tiếp trong code (naming conventions), các đơn vị dữ liệu cơ bản nhất (primitives), sự khác biệt giữa chúng và các đối tượng (wrappers), và cuối cùng là các phép toán cơ bản.

**Áp dụng thực tế:** Nắm vững những điều này giúp bạn:

* **Viết code hiệu năng cao:** Hiểu khi nào nên dùng `int` thay vì `Integer` để tránh overhead không cần thiết trong các vòng lặp xử lý hàng triệu phần tử.
* **Tránh các lỗi tinh vi:** Ngăn chặn `NullPointerException` do unboxing, hay lỗi logic do so sánh đối tượng bằng `==` thay vì `equals()`.
* **Làm việc nhóm hiệu quả:** Code tuân thủ naming convention giúp đồng nghiệp đọc hiểu và bảo trì code của bạn dễ dàng hơn.
* **Troubleshoot hiệu quả:** Đọc hiểu GC logs để chẩn đoán các vấn_đề về bộ nhớ và hiệu năng của ứng dụng trên môi trường sản xuất (production).

---

### 2. Giải thích chi tiết

#### **1. Garbage Collection (GC) - Người dọn rác tự động**

* **Định nghĩa ngắn gọn:** Garbage Collection (GC) là một cơ chế quản lý bộ nhớ tự động của JVM. Nó theo dõi các đối tượng được tạo trên **Heap** và tự động giải phóng bộ nhớ của các đối tượng không còn được sử dụng (không còn tham chiếu nào trỏ tới).

* **Cơ chế làm việc:**
  * **Vùng nhớ Heap:** Là khu vực bộ nhớ chính mà JVM dùng để cấp phát cho các đối tượng. Heap được chia thành các thế hệ (Generations):
        ***Young Generation (Thế hệ trẻ):** Nơi các đối tượng mới được tạo ra. Vùng này lại được chia nhỏ thành *Eden* và hai *Survivor spaces (S0, S1)*. Hầu hết đối tượng sẽ "chết" ở đây.
        * **Old Generation (Thế hệ già):** Nơi chứa các đối tượng đã sống sót qua nhiều chu kỳ dọn rác ở Young Generation.
    * **Minor GC vs. Major GC:**
      * **Minor GC:** Diễn ra thường xuyên ở Young Generation. Nhanh và ít ảnh hưởng.
        * **Major GC (hoặc Full GC):** Diễn ra ở Old Generation (và thường bao gồm cả Young Gen). Chậm hơn đáng kể và có thể gây tạm dừng ứng dụng.
    * **Stop-The-World & Safepoint:** Để đảm bảo an toàn khi dọn dẹp, GC cần tạm dừng tất cả các luồng ứng dụng (application threads). Khoảng thời gian tạm dừng này gọi là **Stop-The-World (STW)**. Việc tạm dừng chỉ có thể xảy ra tại các điểm an toàn gọi là **Safepoint**. Giảm thiểu tần suất và thời gian của STW là mục tiêu chính của các GC hiện đại.
    * **Thuật toán cơ bản:**
      * **Mark–Sweep:** **Đánh dấu (Mark):** Bắt đầu từ các "GC Roots" (ví dụ: biến local, luồng đang chạy), GC đi theo các tham chiếu và đánh dấu tất cả các đối tượng có thể truy cập là "còn sống". **Quét (Sweep):** Toàn bộ heap được quét, và bất kỳ đối tượng nào không được đánh dấu sẽ bị thu gom.
        * **Compacting:** Một biến thể của Mark-Sweep, sau khi quét, các đối tượng còn sống sẽ được di chuyển lại gần nhau để giảm phân mảnh bộ nhớ (fragmentation).
    * **Các loại GC phổ biến (mức khái niệm):**
      * **G1 GC (Garbage-First):** Mặc định từ Java 9. Chia heap thành nhiều vùng (regions) nhỏ và ưu tiên dọn dẹp các vùng chứa nhiều "rác" nhất. Cân bằng tốt giữa throughput và latency.
        * **ZGC / Shenandoah:** Các GC có độ trễ cực thấp (low-pause), thực hiện hầu hết công việc song song với các luồng ứng dụng, giảm thiểu thời gian STW xuống mức mili-giây, phù hợp cho các ứng dụng yêu cầu phản hồi siêu nhanh.
    * **Khi nào GC chạy?** Thường là khi một vùng nhớ (như Eden) bị đầy và JVM cần cấp phát thêm bộ nhớ cho đối tượng mới.
    * **GC Logs cơ bản:** Có thể bật bằng cờ JVM: `-Xlog:gc*`. Log sẽ cho biết loại GC (Minor/Major), thời gian tạm dừng, lượng bộ nhớ được giải phóng.

* **Bẫy thường gặp:**
  * Tạo ra quá nhiều đối tượng tồn tại lâu (long-lived objects) làm đầy Old Gen, dẫn đến Full GC thường xuyên và làm ứng dụng bị "khựng".
    * Gọi `System.gc()` một cách vô tội vạ. Lệnh này chỉ là một "gợi ý" cho JVM, không đảm bảo GC sẽ chạy, và có thể gây ra Full GC không cần thiết, làm giảm hiệu năng.

* **Best Practices:**
  * **Viết code tiết kiệm bộ nhớ:** Tái sử dụng đối tượng khi có thể, chọn kiểu dữ liệu phù hợp.
    * **Tránh `System.gc()`:** Hãy để JVM tự quyết định khi nào cần dọn rác.
    * **Chọn đúng GC:** Tùy vào đặc tính của ứng dụng (ưu tiên throughput hay latency) để chọn GC phù hợp (G1, ZGC...).

#### **2. Từ khóa (Keywords) trong Java**

Đây là những từ dành riêng cho trình biên dịch, có ý nghĩa đặc biệt và không được dùng làm tên biến, tên phương thức hay tên lớp.

| Nhóm                 | Keywords                                                                               | Định nghĩa ngắn gọn & Ví dụ                                                               |
| :------------------- | :------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------- |
| **Điều khiển luồng** | `if`, `else`, `switch`, `case`, `default`, `for`, `while`, `do`, `break`, `continue`, `return` | Điều hướng cách thực thi mã. `for (int i=0; i<5; i++) { if (i==3) continue; System.out.println(i); }` |
| **Khai báo** | `class`, `interface`, `enum`, `package`, `import`, `extends`, `implements`, `new`, `this`, `super`, `void`, `var` | Định nghĩa cấu trúc và tạo đối tượng. `public class Car extends Vehicle { ... }`              |
| **Truy cập** | `public`, `protected`, `private`, `final`, `static`, `abstract`, `transient`, `volatile`, `synchronized` | Quy định phạm vi truy cập và hành vi của thành viên. `private final String id;`             |
| **Xử lý lỗi** | `try`, `catch`, `finally`, `throw`, `throws`, `assert`                                 | Quản lý các tình huống ngoại lệ. `try { ... } catch (Exception e) { throw new RuntimeException(e); }` |

* **Bẫy thường gặp:** Dùng `assert` để kiểm tra logic nghiệp vụ. `assert` có thể bị tắt khi chạy ứng dụng, làm mất đi các bước kiểm tra quan trọng.
* **Best Practices:** Dùng `final` cho các biến không cần thay đổi giá trị để tăng tính bất biến (immutability) và giúp trình biên dịch tối ưu hóa.

#### **3. Naming Convention - Quy tắc đặt tên**

Quy tắc đặt tên giúp code dễ đọc và dễ hiểu, là một phần cực kỳ quan trọng của "code sạch".

| Loại                    | Quy tắc                  | Ví dụ đúng                      | Ví dụ sai                 |
| :---------------------- | :---------------------- | :------------------------------ | :------------------------ |
| **Class, Interface, Enum** | UpperCamelCase (PascalCase) | `UserService`, `Runnable`       | `userService`, `runnable` |
| **Object, Biến, Method** | lowerCamelCase          | `userName`, `calculateTotal()`  | `UserName`, `Calculate_Total()` |
| **Constant (hằng số)** | SCREAMING_SNAKE_CASE    | `MAX_CONNECTIONS`, `PI`         | `maxConnections`          |
| **Package** | lowercase, phân tách bằng dấu chấm | `com.google.api.services`       | `com.google.Api.Services` |

* **Quy tắc viết tắt:** Chỉ dùng các từ viết tắt phổ biến (như `Id`, `Xml`, `Http`). Ví dụ: `userId`, `parseXml()`. Không dùng: `calculateSlry()`.
* **Liên hệ OOP:** Naming convention tốt phản ánh rõ ràng mục đích của class/method, góp phần vào tính trừu tượng (abstraction) và dễ đọc của code.

#### **4. Primitive Data Types - Kiểu dữ liệu nguyên thủy**

Đây là 8 kiểu dữ liệu cơ bản nhất, không phải là đối tượng.

| Kiểu      | Kích thước (bit) | Phạm vi giá trị (xấp xỉ)              | Giá trị mặc định | Ghi chú                                                                    |
| :-------- | :---------------- | :------------------------------------- | :---------------- | :------------------------------------------------------------------------- |
| `byte`    | 8                 | -128 đến 127                          | `0`               | Dùng cho dữ liệu stream, file.                                             |
| `short`   | 16                | -32,768 đến 32,767                      | `0`               | Ít dùng, có thể tiết kiệm bộ nhớ trong mảng lớn.                              |
| `int`     | 32                | -2.1 tỷ đến 2.1 tỷ                    | `0`               | **Lựa chọn mặc định** cho số nguyên.                                       |
| `long`    | 64                | -9x10¹⁸ đến 9x10¹⁸                      | `0L`              | Dùng cho ID, timestamp, số lớn. Hậu tố `L`.                                  |
| `float`   | 32                | ±3.4x10³⁸ (6-7 chữ số thập phân)        | `0.0f`            | Dùng cho đồ họa, tính toán khoa học không yêu cầu độ chính xác cao. Hậu tố `f`. |
| `double`  | 64                | ±1.7x10³⁰⁸ (15 chữ số thập phân)         | `0.0d`            | **Lựa chọn mặc định** cho số thực. Dùng trong tính toán tài chính (cần cẩn thận) hoặc khoa học. |
| `boolean` | ~1 (phụ thuộc JVM)  | `true`, `false`                        | `false`           | Biểu diễn giá trị logic.                                                   |
| `char`    | 16                | 0 đến 65,535 (Unicode)                  | `'\u0000'`        | Biểu diễn một ký tự.                                                       |

* **Hiệu năng/Bộ nhớ:** Primitives rất nhanh và tốn ít bộ nhớ vì chúng được lưu trực tiếp trên **stack** (với biến cục bộ) và không có overhead của một đối tượng (như header).

#### **5. So sánh Primitive vs. Object (Wrapper)**

Mỗi kiểu primitive có một lớp "bọc" (wrapper) tương ứng (ví dụ: `int` -> `Integer`).

| Tiêu chí                | Primitive (e.g., `int`)           | Object/Wrapper (e.g., `Integer`)                                          |
| :---------------------- | :-------------------------------- | :------------------------------------------------------------------------ |
| **Hiệu năng** | **Rất cao**. Truy cập trực tiếp giá trị. | **Thấp hơn**. Cần giải tham chiếu (dereference) để lấy giá trị.              |
| **Bộ nhớ** | **Ít**. Chỉ tốn bộ nhớ cho giá trị.   | **Nhiều hơn**. Tốn bộ nhớ cho header của đối tượng + giá trị.              |
| **Nullability** | Không thể `null`.                 | **Có thể `null`**.                                                        |
| **Dùng trong Collections** | Không thể.                       | **Bắt buộc**. `List<Integer>`, không thể `List<int>`.                     |
| **Giá trị mặc định** | `0`, `false`...                   | `null`                                                                    |
| **So sánh** | Dùng `==` để so sánh giá trị.     | Dùng `.equals()` để so sánh giá trị, `==` so sánh tham chiếu (địa chỉ bộ nhớ). |
| **Overhead** | Không có.                         | Có **Boxing/Unboxing overhead**.                                          |

* **Best Practices:** **Ưu tiên sử dụng primitives** trừ khi bạn cần một trong các tính năng của wrapper: khả năng `null`, hoặc dùng trong các cấu trúc dữ liệu generics (Collections, Map).

#### **6. So sánh `int` vs. `Integer`**

Đây là cặp phổ biến nhất để minh họa sự khác biệt.

| Tiêu chí              | `int`                                     | `Integer`                                                                                                                                                             |
| :-------------------- | :---------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bộ nhớ** | 4 bytes.                                  | ~16 bytes trên JVM 64-bit (12-byte header + 4-byte value).                                                                                                            |
| **Null** | Không thể `null`. Gây lỗi biên dịch.      | Có thể `null`.                                                                                                                                                        |
| **Autoboxing** | Tự động "bọc" thành `Integer` khi cần.    | Tự động "mở" thành `int` khi cần (unboxing).                                                                                                                         |
| **So sánh giá trị** | `int a = 100; int b = 100; a == b;` (true) | `Integer a = 100; Integer b = 100; a.equals(b);` (true)                                                                                                              |
| **So sánh tham chiếu** | Không áp dụng.                            | `Integer a = new Integer(100); Integer b = new Integer(100); a == b;` (**false**)                                                                                     |
| **Cache** | Không áp dụng.                            | **JVM cache các đối tượng `Integer` từ -128 đến 127**. `Integer a = 127; Integer b = 127; a == b;` (**true**). `Integer c = 128; Integer d = 128; c == d;` (**false**). |

* **Bẫy thường gặp (Cực kỳ phổ biến):** Dùng `==` để so sánh hai đối tượng `Integer`. Nó chỉ đúng với các giá trị trong cache (-128 đến 127). Luôn luôn dùng `.equals()` khi bạn muốn so sánh giá trị của hai đối tượng.

#### **7. Autoboxing & Unboxing**

* **Định nghĩa:**
  * **Autoboxing:** Quá trình tự động chuyển đổi một giá trị primitive thành đối tượng wrapper tương ứng.
    * **Unboxing:** Quá trình tự động chuyển đổi một đối tượng wrapper thành giá trị primitive tương ứng.
* **Cơ chế biên dịch:** Trình biên dịch Java tự động chèn các lời gọi phương thức cần thiết.
  * Autoboxing: `Integer i = 100;` -> `Integer i = Integer.valueOf(100);`
    * Unboxing: `int j = i;` -> `int j = i.intValue();`
* **Tác động hiệu năng:** Việc tạo đối tượng trong autoboxing có thể gây tốn kém, đặc biệt là trong các vòng lặp chặt (tight loops). Nó tạo áp lực lên GC.
* **Bẫy NPE khi Unbox null (Lỗi kinh điển):**

    ```java
    public class UnboxingNPE {
        public static void main(String[] args) {
            Integer myNumber = null;
            // Dòng này sẽ ném ra NullPointerException
            // Vì nó tương đương với: int primitive = myNumber.intValue();
            // và bạn không thể gọi phương thức trên một tham chiếu null.
            int primitive = myNumber;
        }
    }
    ```

* **Best Practices:** Trước khi thực hiện một phép toán hoặc gán một wrapper cho một primitive, hãy luôn kiểm tra `null`. `if (myNumber != null) { ... }`

#### **8. Thực hành Autoboxing/Unboxing**

```java
public class AutoboxingPractice {
    public static void main(String[] args) {
        // Bài 1: Cơ bản
        Integer wrapper = 10; // Autoboxing
        int primitive = wrapper; // Unboxing
        System.out.println("Bài 1: " + (wrapper == primitive)); // Output: true
        // Giải thích: Khi so sánh wrapper và primitive, wrapper sẽ được unbox.
        // Phép so sánh trở thành 10 == 10.

        // Bài 2: Tác động trong tính toán
        Integer a = 5;
        Integer b = 10;
        Integer result = a + b; // Unboxing a, b; tính tổng; rồi autoboxing kết quả.
        System.out.println("Bài 2: " + result); // Output: 15

        // Bài 3: Bẫy NullPointerException
        Integer npeSource = null;
        try {
            int target = npeSource; // Cố gắng unbox một giá trị null
        } catch (NullPointerException e) {
            System.out.println("Bài 3: Đã bắt được NullPointerException!");
        }
    }
}
````

#### **9. Toán tử gán & so sánh**

* `=`: **Toán tử gán**. Gán giá trị bên phải cho biến bên trái.
* `+=`, `-=`, `*=`, `/=`, `%=`: **Toán tử gán kết hợp**. `x += 5;` là viết tắt của `x = x + 5;`.
* `==`: **Toán tử so sánh bằng**.
  * Với primitives: So sánh **giá trị**. `10 == 10` là true.
  * Với objects: So sánh **tham chiếu (địa chỉ bộ nhớ)**. Hai đối tượng khác nhau sẽ có tham chiếu khác nhau, ngay cả khi chúng có cùng nội dung.
* `!=`: **Toán tử so sánh không bằng**. Ngược lại với `==`.
* `.equals(Object obj)`: **Phương thức so sánh**.
  * Được định nghĩa trong lớp `Object`. Các lớp như `String`, `Integer` đã override nó để so sánh **nội dung/trạng thái logic** của đối tượng.
  * **Best practice:** Luôn dùng `.equals()` để so sánh nội dung của các đối tượng.
* **Thứ tự đánh giá:** Các biểu thức được đánh giá từ trái qua phải, tuân theo độ ưu tiên của toán tử.

#### **10. Thực hành gán & so sánh**

````java
public class ComparisonPractice {
    public static void main(String[] args) {
        // Bài 1: Lỗi logic với Integer
        Integer val1 = 200;
        Integer val2 = 200;
        System.out.println("So sánh Integer > 127 với == : " + (val1 == val2)); // Output: false
        // Giải thích: 200 nằm ngoài cache, `new Integer(200)` được gọi, tạo 2 đối tượng khác nhau.
        System.out.println("So sánh Integer với .equals() : " + val1.equals(val2)); // Output: true
        // Giải thích: .equals() so sánh giá trị bên trong (200 == 200).

        // Bài 2: Kịch bản benchmark nhỏ (minh họa ý tưởng)
        long startTime = System.nanoTime();
        Long sumPrimitive = 0L;
        for (long i = 0; i < 1_000_000; i++) {
            sumPrimitive += i; // Nhanh, không tạo đối tượng
        }
        long endTime = System.nanoTime();
        System.out.println("Vòng lặp primitive (long): " + (endTime - startTime) + " ns");

        startTime = System.nanoTime();
        Long sumWrapper = 0L;
        for (long i = 0; i < 1_000_000; i++) {
            // Autoboxing: `i` thành `Long` để cộng, rồi unboxing để gán lại cho sumWrapper. Rất chậm.
            // Chính xác là `sumWrapper = Long.valueOf(sumWrapper.longValue() + i);`
            sumWrapper = sumWrapper + i;
        }
        endTime = System.nanoTime();
        System.out.println("Vòng lặp wrapper (Long) : " + (endTime - startTime) + " ns");
        // Output: Vòng lặp wrapper chậm hơn đáng kể.
    }
}
````

#### **11. Toán tử toán học**

* `+`, `-`, `*`, `/`: Cộng, trừ, nhân, chia.
* `%`: **Modulo**. Phép chia lấy phần dư. `10 % 3` là `1`.
* `++`, `--`: Tăng/giảm 1 đơn vị.
  * `i++` (post-increment): trả về giá trị của `i` *trước khi* tăng.
  * `++i` (pre-increment): trả về giá trị của `i` *sau khi* tăng.
* **Chia số nguyên vs. số thực:**
  * Chia hai số nguyên sẽ cho kết quả là số nguyên (phần thập phân bị cắt bỏ). `5 / 2` là `2`.
  * Để có kết quả số thực, ít nhất một toán hạng phải là số thực. `5.0 / 2` hoặc `(double) 5 / 2` là `2.5`.
* **Tràn số (Overflow):** Khi kết quả của một phép tính vượt quá phạm vi của kiểu dữ liệu.

    ```java
    int max = Integer.MAX_VALUE; // 2,147,483,647
    int overflow = max + 1;
    System.out.println(overflow); // Output: -2,147,483,648 (vòng về giá trị nhỏ nhất)
    ```

* **Quy tắc chuyển kiểu (Type Promotion):** Khi thực hiện phép toán giữa các kiểu dữ liệu khác nhau, Java sẽ tự động "thăng cấp" kiểu nhỏ hơn lên kiểu lớn hơn. `int + long` -\> kết quả là `long`.

-----

### 3\. Bài tập thực hành có lời giải

**Bài 1 (Dễ):** Dự đoán đầu ra của đoạn code sau và giải thích.

````java
List<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.add(3);
list.remove(2); // Dòng này làm gì?
System.out.println(list);
````

**Lời giải:**

* Output: `[1, 2]`
* Giải thích: `list.remove(2)` gọi phương thức `remove(int index)`, không phải `remove(Object o)`. Nó xóa phần tử tại *chỉ số (index)* 2, tức là số `3`. Nếu muốn xóa đối tượng có giá trị là `2`, phải viết `list.remove(Integer.valueOf(2))`. Đây là một bẫy phổ biến của autoboxing.

**Bài 2 (Trung bình):** Sửa lỗi trong đoạn code sau để nó hoạt động đúng ý định (so sánh nội dung chuỗi).

````java
String s1 = new String("test");
String s2 = new String("test");
System.out.println("s1 == s2 is: " + (s1 == s2)); // Ý định là true
````

**Lời giải:**

* Sửa lỗi: Thay `==` bằng `.equals()`.

    ````java
    System.out.println("s1.equals(s2) is: " + s1.equals(s2)); // Output: true
    ````

* Giải thích: `new String(...)` luôn tạo một đối tượng mới trên heap. `s1` và `s2` là hai tham chiếu khác nhau trỏ đến hai đối tượng khác nhau, do đó `s1 == s2` là `false`. `.equals()` so sánh nội dung ký tự của chuỗi nên cho kết quả `true`.

**Bài 3 (Trung bình):** Đoạn code sau sẽ in ra gì?

````java
Integer a = 1000, b = 1000;
Integer c = 100, d = 100;
System.out.println(a == b);
System.out.println(c == d);
````

**Lời giải:**

* Output:
    ````
    false
    true
    ````

* Giải thích: `a == b` là `false` vì 1000 nằm ngoài dải cache (-128 đến 127), nên hai đối tượng `Integer` riêng biệt được tạo ra. `c == d` là `true` vì 100 nằm trong dải cache, JVM tái sử dụng cùng một đối tượng `Integer` cho cả `c` và `d`.

**Bài 4 (Khó):** Viết một hàm `multiplySafe(int a, int b)` trả về `long` là kết quả của `a * b` và không bị tràn số.

**Lời giải:**

````java
public long multiplySafe(int a, int b) {
    return (long) a * b;
}
````

* Giải thích: Bằng cách ép kiểu (`cast`) một trong hai toán hạng `int` thành `long` *trước khi* thực hiện phép nhân, toàn bộ biểu thức sẽ được thăng cấp để tính toán trên kiểu `long`. Kết quả `long` có đủ không gian để chứa tích của hai số `int` mà không bị tràn.

**Bài 5 (Khó):** Đoạn code sau sẽ gây ra lỗi gì và tại sao? Làm thế nào để sửa?

````java
public Integer getStatusCode() {
    // Giả sử logic phức tạp có thể trả về null
    return null;
}

public void process() {
    int code = getStatusCode(); // Dòng gây lỗi
    if (code == 200) {
        System.out.println("OK");
    }
}
````

**Lời giải:**

* Lỗi: `NullPointerException` tại dòng `int code = getStatusCode();`.
* Tại sao: Hàm `getStatusCode()` trả về một `Integer` null. Khi gán nó cho một biến `int` (primitive), Java cố gắng thực hiện unboxing (`null.intValue()`), dẫn đến NPE.
* Cách sửa: Luôn kiểm tra null trước khi unboxing.

    ````java
    public void process() {
        Integer codeWrapper = getStatusCode();
        if (codeWrapper != null && codeWrapper == 200) { // Unboxing an toàn
            System.out.println("OK");
        }
    }
    ````

**Bài 6 (Tư duy GC):** Giữa việc tạo 1 triệu đối tượng nhỏ và giải phóng chúng ngay lập tức, và việc tạo 100 đối tượng lớn và giữ chúng trong suốt thời gian chạy của ứng dụng, kịch bản nào có khả năng gây ra `Full GC` cao hơn?

**Lời giải:**

* Kịch bản thứ hai (100 đối tượng lớn, sống lâu) có khả năng gây ra `Full GC` cao hơn.
* Giải thích: 1 triệu đối tượng nhỏ, sống ngắn sẽ được tạo trong Young Generation và bị dọn dẹp hiệu quả bởi các chu kỳ `Minor GC`. Chúng hiếm khi sống sót đủ lâu để được chuyển sang Old Generation. Ngược lại, 100 đối tượng lớn, sống lâu sẽ nhanh chóng được thăng cấp lên Old Generation. Theo thời gian, chúng sẽ làm đầy Old Gen, buộc JVM phải thực hiện một chu kỳ `Major/Full GC` tốn kém để dọn dẹp.

-----

### 4\. Bộ kiểm tra nhanh (Quiz)

1. **Kết quả của `9 / 2` trong Java là gì?**
    a) 4.5
    b) 4
    c) 5
    d) Lỗi biên dịch

2. **Dòng code nào sau đây gây ra `NullPointerException`?**
    a) `Integer x = null;`
    b) `String s = null;`
    c) `int y = (Integer) null;`
    d) `if (null == null)`

3. **Để so sánh nội dung của hai đối tượng `String s1` và `String s2`, bạn nên dùng:**
    a) `s1 == s2`
    b) `s1.compare(s2)`
    c) `s1.equals(s2)`
    d) `s1 = s2`

4. **Kiểu dữ liệu nào sau đây không phải là primitive type?**
    a) `long`
    b) `String`
    c) `boolean`
    d) `double`

5. **`Integer x = 128; Integer y = 128;` Biểu thức `x == y` trả về giá trị gì?**
    a) true
    b) false
    c) Lỗi biên dịch
    d) Ném ra exception

6. **"Stop-the-world" trong GC có nghĩa là gì?**
    a) JVM ngừng hoạt động hoàn toàn.
    b) Tất cả các luồng ứng dụng bị tạm dừng.
    c) Chỉ có luồng chính bị tạm dừng.
    d) Thế giới ngừng quay.

7. **Quy tắc đặt tên nào là đúng cho một hằng số (constant)?**
    a) `myConstant`
    b) `MyConstant`
    c) `MY_CONSTANT`
    d) `my-constant`

8. **Giá trị mặc định của một biến `boolean` thành viên của lớp là gì?**
    a) `true`
    b) `false`
    c) `null`
    d) `0`

9. **Mục đích chính của vùng nhớ "Old Generation" trong heap là gì?**
    a) Chứa các đối tượng mới được tạo.
    b) Chứa các đối tượng đã sống sót qua nhiều chu kỳ GC.
    c) Chứa metadata của các lớp.
    d) Không được sử dụng trong các JVM hiện đại.

10. **Autoboxing là quá trình chuyển đổi từ:**
    a) `Object` sang `String`
    b) `int` sang `Integer`
    c) `Integer` sang `int`
    d) `byte[]` sang `String`

**Đáp án:** 1-b, 2-c, 3-c, 4-b, 5-b, 6-b, 7-c, 8-b, 9-b, 10-b

-----

### 5\. Checklists tóm tắt để ôn thi/phỏng vấn

**Garbage Collection:**

* [ ] Phân biệt Heap vs. Stack.
* [ ] Giải thích Young Gen (Eden, S0/S1) và Old Gen.
* [ ] Định nghĩa Minor GC và Major/Full GC.
* [ ] Hiểu khái niệm "Stop-the-world" và tại sao nó quan trọng.
* [ ] Kể tên ít nhất 2 thuật toán GC (ví dụ: Mark-Sweep).
* [ ] Biết tên các GC hiện đại (G1, ZGC).
* [ ] Biết tại sao `System.gc()` là một anti-pattern.

**Primitives & Wrappers:**

* [ ] Liệt kê đủ 8 kiểu primitive.
* [ ] Nắm rõ sự khác biệt: hiệu năng, bộ nhớ, nullability.
* [ ] Biết khi nào bắt buộc phải dùng wrapper (ví dụ: Collections).
* [ ] Giải thích cơ chế Autoboxing/Unboxing.
* [ ] Nhận diện và phòng tránh bẫy `NullPointerException` khi unboxing.
* [ ] Giải thích cơ chế `Integer` cache (-128 đến 127).

**Toán tử & So sánh:**

* [ ] Phân biệt rõ ràng `==` (so sánh tham chiếu cho đối tượng) và `.equals()` (so sánh nội dung).
* [ ] Luôn dùng `.equals()` để so sánh `String`, `Integer` và các đối tượng khác.
* [ ] Hiểu kết quả của phép chia số nguyên (ví dụ: `5/2=2`).
* [ ] Nhận biết nguy cơ tràn số (overflow) và cách phòng tránh (dùng kiểu dữ liệu lớn hơn).
* [ ] Phân biệt `i++` và `++i`.

**Quy tắc chung:**

* [ ] Nắm vững naming convention cho Class, method, variable, constant.
* [ ] Biết các nhóm keyword chính (điều khiển luồng, truy cập...).
* [ ] Hiểu từ khóa `final` có thể áp dụng cho biến, phương thức, và lớp.

-----

### 6\. Mẫu mã nguồn hợp nhất

````java
import java.util.ArrayList;
import java.util.List;

/**
 * Đây là một lớp tổng hợp các khái niệm Java cơ bản đã thảo luận.
 * Chạy hàm main() để xem kết quả minh họa.
 */
public class JavaFundamentalsDemo {

    // 3. Naming convention: Hằng số dùng SCREAMING_SNAKE_CASE
    private static final int MAX_ITERATIONS = 1_000;

    public static void main(String[] args) {
        System.out.println("===== Bắt đầu Demo các khái niệm Java cơ bản =====");

        demonstratePrimitivesAndWrappers();
        demonstrateComparisonPitfalls();
        demonstrateAutoboxing();
        demonstrateOperators();
        demonstrateGCPressureConcept();

        System.out.println("===== Kết thúc Demo =====");
    }

    /**
     * Minh họa các kiểu dữ liệu nguyên thủy, wrappers, và sự khác biệt về giá trị mặc định.
     */
    public static void demonstratePrimitivesAndWrappers() {
        System.out.println("\n--- 4, 5. Primitives vs. Wrappers ---");
        // 4. Primitive types: có giá trị mặc định là 0 hoặc false
        int primitiveInt = 0;
        double primitiveDouble = 0.0;
        // 5. Wrapper types: có giá trị mặc định là null
        Integer wrapperInt = null;
        System.out.println("Giá trị mặc định của int: " + primitiveInt);
        System.out.println("Giá trị mặc định của Integer: " + wrapperInt);
    }

    /**
     * Chỉ ra bẫy phổ biến khi so sánh đối tượng bằng ==.
     */
    public static void demonstrateComparisonPitfalls() {
        System.out.println("\n--- 6, 9, 10. So sánh (== vs .equals() và Integer Cache) ---");
        // Integer cache hoạt động cho giá trị từ -128 đến 127
        Integer cachedA = 100;
        Integer cachedB = 100;
        System.out.println("So sánh 100 == 100: " + (cachedA == cachedB)); // true, vì dùng cache

        // Giá trị ngoài cache sẽ tạo đối tượng mới
        Integer nonCachedA = 200;
        Integer nonCachedB = 200;
        System.out.println("So sánh 200 == 200: " + (nonCachedA == nonCachedB)); // false, vì là 2 đối tượng khác nhau

        // Best practice: Luôn dùng .equals() để so sánh giá trị đối tượng
        System.out.println("So sánh 200.equals(200): " + nonCachedA.equals(nonCachedB)); // true
    }

    /**
     * Minh họa Autoboxing, Unboxing và bẫy NullPointerException.
     */
    public static void demonstrateAutoboxing() {
        System.out.println("\n--- 7, 8. Autoboxing & Unboxing ---");
        List<Integer> numbers = new ArrayList<>();
        // Autoboxing: `int` 10 được tự động chuyển thành `Integer`
        numbers.add(10);
        // Unboxing: `numbers.get(0)` trả về `Integer`, được chuyển thành `int` để cộng
        int value = numbers.get(0) + 5;
        System.out.println("Kết quả unboxing và tính toán: " + value);

        // Bẫy NPE
        Integer nullInteger = null;
        try {
            // Cố gắng unbox một `Integer` null để gán cho `int`
            int npe = nullInteger;
        } catch (NullPointerException e) {
            System.out.println("Bẫy đã xảy ra: Bắt được NullPointerException khi unbox null!");
        }
    }

    /**
     * Minh họa các toán tử toán học, chia số nguyên và tràn số.
     */
    public static void demonstrateOperators() {
        System.out.println("\n--- 11. Toán tử toán học ---");
        // Chia số nguyên
        int intDivision = 5 / 2;
        System.out.println("Chia số nguyên 5 / 2 = " + intDivision); // Kết quả là 2

        // Để có kết quả đúng, hãy cast
        double floatDivision = (double) 5 / 2;
        System.out.println("Chia số thực (double) 5 / 2 = " + floatDivision); // Kết quả là 2.5

        // Tràn số (Overflow)
        int maxInt = Integer.MAX_VALUE;
        System.out.println("Giá trị lớn nhất của int: " + maxInt);
        System.out.println("maxInt + 1 = " + (maxInt + 1)); // Bị tràn và quay vòng về số âm
    }

    /**
     * Mô phỏng việc tạo áp lực lên Garbage Collector.
     */
    public static void demonstrateGCPressureConcept() {
        System.out.println("\n--- 1. Khái niệm về áp lực GC ---");
        // Vòng lặp này tạo ra nhiều đối tượng tồn tại trong thời gian ngắn (short-lived)
        // Những đối tượng này sẽ được dọn dẹp hiệu quả bởi Minor GC trong Young Generation.
        for (int i = 0; i < MAX_ITERATIONS; i++) {
            String tempObject = "Object " + i;
            // Đối tượng tempObject không còn được tham chiếu sau vòng lặp và trở thành "rác".
        }
        System.out.println(MAX_ITERATIONS + " đối tượng tạm thời đã được tạo và đủ điều kiện để GC.");
        // Gợi ý GC (không nên dùng trong code production)
        // System.gc();
    }
}
````

-----

### 7\. Hiệu năng & Thực tiễn sản xuất

* **Boxing Overhead:** Tránh autoboxing/unboxing trong các đoạn code yêu cầu hiệu năng cao như vòng lặp xử lý dữ liệu lớn, game loops, hay các thuật toán phức tạp. Mỗi lần boxing là một lần tạo đối tượng mới, gây áp lực lên GC và làm chậm quá trình thực thi.
* **`equals()` vs `==`:** Đây là nguồn gây lỗi logic phổ biến nhất cho người mới bắt đầu. Luôn ghi nhớ: `==` cho primitives, `.equals()` cho objects. Việc dùng `==` sai trên đối tượng có thể dẫn đến các bug khó tìm, chỉ xảy ra ở một số điều kiện nhất định (ví dụ: khi giá trị nằm ngoài cache của `Integer`).
* **Lựa chọn kiểu dữ liệu:** Đừng lãng phí bộ nhớ. Nếu bạn biết một giá trị sẽ không bao giờ vượt quá 100, dùng `byte` thay vì `int` trong một mảng lớn có thể tiết kiệm đáng kể bộ nhớ. Tuy nhiên, `int` thường là lựa chọn cân bằng nhất về hiệu năng trên hầu hết các kiến trúc CPU.
* **Khuyến nghị cho code hiệu năng cao:**
    1. **Ưu tiên Primitives:** Dùng `int`, `long`, `double` thay vì các lớp wrapper của chúng trong các phép tính.
    2. **Khởi tạo với kích thước hợp lý:** Khi dùng `ArrayList` hoặc `HashMap`, nếu bạn biết trước số lượng phần tử, hãy khởi tạo chúng với capacity ban đầu (`new ArrayList<>(10000)`) để tránh việc phải thay đổi kích thước mảng nội bộ liên tục.
    3. **Tái sử dụng đối tượng:** Tránh tạo đối tượng mới trong vòng lặp. Nếu có thể, hãy tạo một lần bên ngoài và cập nhật trạng thái của nó bên trong.

-----