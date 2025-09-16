# 1) Attribute/Field vs Method — định nghĩa, lifetime, nơi lưu, instance vs static

**Field (thuộc tính, biến thành viên)**

* **Định nghĩa:** biến được khai báo trực tiếp trong thân lớp (ngoài thân phương thức/constructor/init block).
* **Lifetime:**

  * *Instance field:* sống từ khi `new` tạo đối tượng đến khi đối tượng bị GC.
  * *Static field:* sống từ khi lớp được *class loader* nạp (lần đầu tiên được dùng) đến khi lớp bị unload (thường là lúc JVM kết thúc).
* **Nơi lưu:**

  * Đối tượng (và **instance fields**) nằm trên **heap**.
  * **Static fields** thuộc “per-class” method area

---

# 2. Inheritance trong Java

### Định nghĩa

* **Inheritance (Kế thừa)**: cơ chế cho phép một lớp (`subclass`/child) kế thừa các thuộc tính (fields) và phương thức (methods) từ một lớp khác (`superclass`/parent).
* Java dùng từ khóa `extends`.
* Ý nghĩa: **tái sử dụng code**, **xây dựng phân cấp tự nhiên** (hierarchy), **thể hiện quan hệ “is-a”**.

### Ý nghĩa thiết kế

* Khi **subclass thật sự là một dạng (specialization)** của superclass.
* Giúp **giảm trùng lặp code** nhưng dễ tạo ràng buộc cứng nhắc nếu lạm dụng.
* Chỉ nên dùng khi mối quan hệ **is-a** rõ ràng.

---

# 2. “is-a” vs “has-a”

### Định nghĩa

* **is-a**: subclass là một specialization của superclass.
  → dùng **Inheritance**.
  Ví dụ: `Car is a Vehicle`.
* **has-a**: một đối tượng chứa đối tượng khác như thành phần.
  → dùng **Composition/Aggregation**.
  Ví dụ: `Car has an Engine`.

---

# 3. Bảng so sánh

| Tiêu chí                     | “is-a” (Inheritance)                                  | “has-a” (Composition)                                              |
| ---------------------------- | ----------------------------------------------------- | ------------------------------------------------------------------ |
| **Tái sử dụng code**         | Cao (thừa hưởng toàn bộ fields/methods)               | Chỉ sử dụng qua delegation (gọi phương thức của đối tượng chứa)    |
| **Đóng gói (Encapsulation)** | Dễ rò rỉ, subclass thấy quá nhiều chi tiết superclass | Tốt hơn, chỉ expose thành phần cần thiết                           |
| **Linh hoạt**                | Kém, subclass bị ràng buộc chặt vào superclass        | Cao, có thể thay đổi thành phần bên trong mà không ảnh hưởng nhiều |
| **Thay đổi yêu cầu**         | Khó sửa nếu superclass thay đổi (Fragile Base Class)  | Dễ thích nghi (thay thế thành phần)                                |
| **Quan hệ tự nhiên**         | “A là một B” (specialization)                         | “A có một B” (composition)                                         |

---

# 4. Ví dụ minh họa

### Vehicle/Car (is-a)

```java
class Vehicle {
    void move() { System.out.println("Vehicle is moving"); }
}

class Car extends Vehicle {
    void honk() { System.out.println("Car honks"); }
}

public class Demo {
    public static void main(String[] args) {
        Car c = new Car();
        c.move(); // kế thừa từ Vehicle
        c.honk();
    }
}
```

### Car/Engine (has-a)

```java
class Engine {
    void start() { System.out.println("Engine starts"); }
}

class Car {
    private Engine engine = new Engine(); // has-a

    void drive() {
        engine.start(); // delegation
        System.out.println("Car drives");
    }
}
```

### UML ASCII

```
[Vehicle] <|-- [Car]          (is-a)
[Car] *-- [Engine]            (has-a, composition)
```

---

# 5. Pitfalls (cạm bẫy)

* **Fragile Base Class Problem**: thay đổi trong superclass (VD đổi tên method, đổi logic) có thể phá toàn bộ subclasses.
* **Diamond of Death**: vấn đề đa kế thừa (một subclass thừa kế từ 2 superclass có cùng method).
  → Java **cấm đa kế thừa class** để tránh ambiguity.
  → Thay thế bằng **Interface** (đa kế thừa được, vì chỉ chứa abstract methods/default methods).

---

# 6. Bài tập Refactor

### Thiết kế kém (dùng kế thừa sai)

```java
class Engine extends Car {  // ❌ Engine không phải là Car
    void start() { System.out.println("Engine starts"); }
}
```

### Refactor tốt hơn (dùng composition)

```java
class Engine {
    void start() { System.out.println("Engine starts"); }
}

class Car {
    private Engine engine;

    Car(Engine e) { this.engine = e; }

    void drive() {
        engine.start();
        System.out.println("Car drives");
    }
}

public class RefactorDemo {
    public static void main(String[] args) {
        Engine e = new Engine();
        Car c = new Car(e);
        c.drive();
    }
}
```

---

👉 Tóm lại:

* **Inheritance (is-a)**: khi subclass là specialization của superclass.
* **Composition (has-a)**: khi class chỉ chứa hoặc sử dụng class khác.
* Java **ưu tiên Composition > Inheritance** (nguyên tắc “Prefer composition over inheritance”).

---
Ok 👍 mình sẽ dạy bạn **Abstraction trong Java** một cách hệ thống: định nghĩa, so sánh abstract class vs interface, bảng quyết định, ví dụ, pitfalls và bài tập thiết kế.

---

# 1. Khái niệm Abstraction

* **Abstraction (Trừu tượng hóa)**: Ẩn giấu chi tiết cài đặt, chỉ để lộ **cái gì** một đối tượng làm, thay vì **nó làm như thế nào**.
* **Mục tiêu**:

  * Giảm phức tạp → lập trình viên tập trung vào hành vi chính.
  * Cho phép thay đổi implement mà không ảnh hưởng đến code client.
* **Liên hệ với Information Hiding**:

  * *Abstraction*: ẩn **implementation** (cách thức hoạt động).
  * *Encapsulation*: ẩn **state** (dữ liệu bên trong).
  * Cả hai bổ trợ nhau trong OOP.

---

# 2. Abstract Class vs Interface

### Abstract Class

* Có thể chứa **state (fields)** + **abstract methods** + **concrete methods**.
* Hỗ trợ **single inheritance** (một class chỉ extend một abstract class).
* Dùng khi các subclass có **chung cấu trúc** hoặc **chung dữ liệu**.

### Interface

* Định nghĩa **contract** (API) → chỉ mô tả hành vi, không state (ngoại trừ `public static final` constants).
* Từ **Java 8+**:

  * `default methods`: có body, cho phép mở rộng API mà không phá vỡ binary compatibility.
  * `private methods`: tái sử dụng code nội bộ trong interface.
* Hỗ trợ **multiple inheritance** (một class có thể `implements` nhiều interface).
* Dùng khi các class **không liên quan nhau** nhưng cần chung hành vi.

---

# 3. Bảng so sánh khi dùng

| Tiêu chí                 | Abstract Class                             | Interface                                                          |
| ------------------------ | ------------------------------------------ | ------------------------------------------------------------------ |
| **State (fields)**       | Có thể có instance variables               | Chỉ `public static final` constants                                |
| **Kế thừa**              | Single inheritance                         | Multiple inheritance                                               |
| **Triển khai mặc định**  | Cho phép methods có body                   | Từ Java 8: `default` & `static` methods; Java 9: `private` methods |
| **Binary Compatibility** | Thêm method **có thể phá vỡ** subclasses   | Thêm `default method` không phá vỡ API                             |
| **Mở rộng API**          | Khó thay đổi (phải chỉnh subclasses)       | Linh hoạt (có thể thêm default method)                             |
| **Dùng khi**             | Nhiều class có state chung, cấu trúc chung | Các class không liên quan cần chung hành vi (plugin, contract)     |

---

# 4. Ví dụ Code

### Abstract Class

```java
abstract class Shape {
    String color;  // shared state

    Shape(String color) {
        this.color = color;
    }

    abstract double area();  // abstract method

    void describe() {  // concrete method
        System.out.println("A " + color + " shape");
    }
}

class Circle extends Shape {
    double radius;

    Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * radius * radius;
    }
}
```

### Interface

```java
interface Drawable {
    void draw();

    default void printInfo() {
        System.out.println("Drawing something...");
    }
}

class Square implements Drawable {
    int side;

    Square(int side) { this.side = side; }

    @Override
    public void draw() {
        System.out.println("Drawing a square with side " + side);
    }
}
```

---

# 5. Pitfalls

* **Interface “God”**: nhồi quá nhiều methods → mất ý nghĩa contract → khó maintain.
* **Lạm dụng default methods**: biến interface thành nửa class, làm code rối. Default nên dùng để **bảo toàn binary compatibility**, không phải chứa logic phức tạp.
* **Abstract class trap**: dùng abstract class khi thực ra chỉ cần một contract → gây cứng nhắc, khó mở rộng.

---

# 6. Bài tập

## (a) Thiết kế một Plugin SPI bằng Interface

```java
public interface PaymentProvider {
    String getName();
    boolean process(double amount);
}
```

Triển khai:

```java
class PaypalProvider implements PaymentProvider {
    public String getName() { return "PayPal"; }
    public boolean process(double amount) {
        System.out.println("Processing " + amount + " via PayPal");
        return true;
    }
}
```

Loader:

```java
class PaymentService {
    private PaymentProvider provider;

    PaymentService(PaymentProvider provider) {
        this.provider = provider;
    }

    void pay(double amount) {
        if (provider.process(amount))
            System.out.println("Payment successful!");
    }
}
```

---

## (b) Biến thể bằng Abstract Class

```java
abstract class AbstractPaymentProvider {
    String name;

    AbstractPaymentProvider(String name) {
        this.name = name;
    }

    abstract boolean process(double amount);

    void log(String msg) {
        System.out.println("[" + name + "] " + msg);
    }
}

class StripeProvider extends AbstractPaymentProvider {
    StripeProvider() { super("Stripe"); }

    @Override
    boolean process(double amount) {
        log("Processing " + amount);
        return true;
    }
}
```

---

👉 Tóm lại:

* **Interface**: hợp đồng hành vi, linh hoạt, hỗ trợ multiple inheritance.
* **Abstract class**: khi cần state chung hoặc logic mặc định phức tạp.
* Nguyên tắc: *Prefer interfaces for contracts, abstract classes for partial implementation*.

---
Mình phân tích đa kế thừa trong Java thật chi tiết cho bạn theo từng khía cạnh nhé:

---

# 1. **Class ← Class**

* **Chỉ đơn kế thừa:** Trong Java, một lớp chỉ `extends` được **1 class cha**.
* **Lý do:** tránh **“diamond problem”** (xung đột phương thức/field nếu nhiều cha định nghĩa cùng tên).
* **Hậu quả nếu cho phép:**

  * Gây **mơ hồ**: compiler không biết dùng implement nào.
  * Làm **ngôn ngữ phức tạp** hơn (như C++ phải có virtual inheritance để xử lý).
* → Java chọn **đơn giản hóa**: 1 class cha, nhưng bù lại hỗ trợ **nhiều interface** để đạt tính linh hoạt.

---

# 2. **Class + nhiều Interface**

* Một class có thể `implements` **nhiều interface**.
* Nếu có **default methods** trùng nhau giữa 2 interface, thì **xung đột**.
* **Quy tắc giải conflict:**

  * Compiler yêu cầu class **override lại** phương thức đó để chỉ rõ bạn muốn behavior nào.

```java
interface A {
    default void show() { System.out.println("A"); }
}
interface B {
    default void show() { System.out.println("B"); }
}
class C implements A, B {
    @Override
    public void show() {  // resolve conflict
        A.super.show();   // hoặc B.super.show()
    }
}
```

---

# 3. **Interface + Interface**

* Một interface có thể `extends` nhiều interface khác.
* Nếu trùng **default method**, interface con **không bắt buộc** resolve, nhưng bất kỳ **class implement** interface con sẽ phải giải quyết.

```java
interface X {
    default void hi() { System.out.println("X"); }
}
interface Y {
    default void hi() { System.out.println("Y"); }
}
interface Z extends X, Y {
    // không cần resolve ở đây
}
class Demo implements Z {
    @Override
    public void hi() {
        Y.super.hi();  // phải resolve
    }
}
```

---

# 4. **Xung đột default method & resolve**

Các cách resolve:

1. **Override trong class** → được ưu tiên nhất.
2. **Chỉ rõ interface nào** bằng `InterfaceName.super.methodName()`.
3. **Thiết kế lại interface** để tránh trùng default.

---

# 5. **Bảng quy tắc ưu tiên (tên phương thức giống nhau)**

| Trường hợp                                         | Ai được ưu tiên                                  |
| -------------------------------------------------- | ------------------------------------------------ |
| Class cha vs Interface default                     | **Class cha**                                    |
| 2 Interface default trùng nhau                     | Compiler báo lỗi → class implement phải override |
| Interface extends nhiều interface có default trùng | Class implement phải override                    |
| Class override phương thức                         | Luôn thắng tất cả                                |

---

# 6. **Bài tập: Thiết kế mixin bằng interface**

Giả sử bạn có class `User` và muốn “mix-in” hành vi `Loggable` và `Timestamped` mà không cần thêm state vào `User`.

```java
interface Loggable {
    default void log(String msg) {
        System.out.println("[LOG] " + msg);
    }
}
interface Timestamped {
    default void printNow() {
        System.out.println("Now: " + System.currentTimeMillis());
    }
}

class User implements Loggable, Timestamped {
    private String name;
    User(String name) { this.name = name; }

    void work() {
        log(name + " is working");
        printNow();
    }
}

public class Test {
    public static void main(String[] args) {
        User u = new User("Alice");
        u.work();
    }
}
```

* `Loggable` và `Timestamped` hoạt động như **mixin** → thêm behavior mà không đụng đến state của `User`.
* Đây là cách Java “giả lập” đa kế thừa hành vi, nhưng **tránh được diamond problem**.

---
Rồi 👍 mình sẽ giải thích **Encapsulation trong Java** từ nền tảng đến nâng cao, chia rõ từng phần theo checklist của bạn:

---

# 1. Định nghĩa & lợi ích

**Encapsulation (Đóng gói)**:

* Là nguyên tắc OOP giấu chi tiết triển khai bên trong class, chỉ phơi bày interface (API) an toàn để tương tác.
* Thường kết hợp:

  * **private fields** → che giấu dữ liệu.
  * **public/protected methods** → cung cấp cách kiểm soát truy cập.

**Lợi ích:**

1. **Tính bất biến (Immutability):** dữ liệu nội bộ không bị thay đổi lung tung.
2. **Khả năng bảo trì:** dễ thay đổi implement bên trong mà không phá code gọi ngoài.
3. **Khả năng test:** dễ kiểm soát state, dễ mock.
4. **An toàn:** ngăn client code phá vỡ invariants (bất biến logic).

---

# 2. Quy tắc thực hành

✅ **Private fields:** tất cả thuộc tính nên là `private`.
✅ **Getter/Setter khôn ngoan:**

* Không phải field nào cũng cần getter/setter.
* Setter chỉ cung cấp khi thật sự cần thay đổi.
* Với object phức tạp, nên **return copy** hoặc **unmodifiable view** thay vì trả thẳng reference.
  ✅ **Defensive Copying:** khi nhận dữ liệu từ bên ngoài hoặc trả dữ liệu ra ngoài → luôn copy.
  ✅ **Unmodifiable Views:** dùng `Collections.unmodifiableList(...)` hoặc `List.copyOf(...)`.

---

# 3. Ví dụ: Immutable vs Mutable

**Phiên bản mutable (xấu):**

```java
class Money {
    public double amount;   // lộ public field
    public String currency;
}
```

❌ Vấn đề: ai cũng có thể sửa `amount` → vi phạm bất biến.

---

**Phiên bản immutable (tốt):**

```java
final class Money {
    private final double amount;
    private final String currency;

    public Money(double amount, String currency) {
        this.amount = amount;
        this.currency = currency;
    }

    public double getAmount() { return amount; }
    public String getCurrency() { return currency; }

    // không có setter
    // có thể thêm method business logic
    public Money add(Money other) {
        if (!this.currency.equals(other.currency)) {
            throw new IllegalArgumentException("Different currencies");
        }
        return new Money(this.amount + other.amount, this.currency);
    }
}
```

✅ `Money` bất biến → không ai sửa state được sau khi tạo.
✅ Mọi thay đổi tạo **object mới**.

---

# 4. Pitfalls thường gặp

1. **Lộ internal array/list:**

```java
class Student {
    private List<String> subjects;
    public List<String> getSubjects() { return subjects; }  // ❌ lộ reference
}
```

➡️ Bên ngoài có thể `getSubjects().add("Hack")`.

✔ Fix:

```java
public List<String> getSubjects() {
    return List.copyOf(subjects); // unmodifiable
}
```

---

2. **Escape this trong constructor:**

```java
class EventSource {
    EventSource(EventListener l) {
        l.register(this); // ❌ this escape khi object chưa hoàn tất
    }
}
```

➡️ Có thể gọi method trên object chưa fully constructed → bug khó debug.

✔ Fix: tránh `this` escape, đăng ký sau khi object đã tạo xong.

---

# 5. Bài tập: Harden class theo encapsulation

Cho class ban đầu:

```java
class BankAccount {
    public String owner;
    public double balance;
    public List<String> transactions;
}
```

❌ Vấn đề:

* Field `public`.
* `transactions` có thể bị sửa từ ngoài.
* Không có kiểm soát khi rút tiền, nạp tiền.

---

**Yêu cầu:** refactor theo encapsulation chuẩn.

👉 Bài tập cho bạn:

1. Đổi `public` → `private`.
2. Thêm constructor hợp lệ.
3. Viết getter an toàn (`List.copyOf`).
4. Thêm method `deposit`, `withdraw` (validate số tiền).
5. Không cho phép `balance` thay đổi trực tiếp.

---
Rồi 👍 mình sẽ dạy bạn **Polymorphism trong Java** chi tiết từ lý thuyết, cơ chế máy ảo, ví dụ, pitfalls cho tới bài tập thực hành.

---

# 1. Khái niệm Polymorphism

* **Polymorphism (đa hình)**: khả năng một đối tượng cụ thể có thể được xử lý thông qua nhiều “hình thức” khác nhau (thường là qua reference kiểu cha hoặc interface).
* Trong Java:

  * **Compile-time polymorphism**: chọn phương thức tại **biên dịch** (method overloading).
  * **Run-time polymorphism**: chọn phương thức tại **thời gian chạy** dựa vào kiểu **thực tế** của object (method overriding).

---

# 2. Compile-time vs Run-time Dispatch

### Compile-time (Static binding)

* **Method overloading** (cùng tên, khác tham số).
* Trình biên dịch quyết định phiên bản nào sẽ gọi.
* Không phụ thuộc vào object thực tế.

```java
class Printer {
    void print(int x) { System.out.println("int: " + x); }
    void print(String s) { System.out.println("String: " + s); }
}

public class Demo {
    public static void main(String[] args) {
        Printer p = new Printer();
        p.print(5);        // chọn print(int)
        p.print("hello");  // chọn print(String)
    }
}
```

### Run-time (Dynamic dispatch / Late binding)

* **Method overriding**: subclass định nghĩa lại method của superclass.
* JVM quyết định tại runtime dựa vào object thực tế.
* Cơ chế này dùng **vtable** (virtual method table).

**Vtable**:

* Mỗi class có bảng ánh xạ method → địa chỉ thực thi.
* Khi gọi `obj.method()`, JVM tra vtable của class thực tế của `obj`.

---

# 3. Ví dụ với `Shape`

```java
interface Shape {
    double area();
}

class Circle implements Shape {
    double r;
    Circle(double r) { this.r = r; }
    public double area() { return Math.PI * r * r; }
}

class Square implements Shape {
    double s;
    Square(double s) { this.s = s; }
    public double area() { return s * s; }
}

public class Demo {
    public static void main(String[] args) {
        Shape s1 = new Circle(3);  // reference kiểu cha
        Shape s2 = new Square(4);

        System.out.println(s1.area()); // chọn Circle.area()
        System.out.println(s2.area()); // chọn Square.area()
    }
}
```

👉 Ở đây `Shape` chỉ biết có method `area()`, nhưng JVM sẽ **late bind** đến implement thực sự (`Circle` hay `Square`).

---

# 4. Pitfalls (Cạm bẫy)

### (a) Static methods **không đa hình**

* Static methods **gắn với class**, không override được → chỉ **method hiding**.

```java
class A {
    static void hello() { System.out.println("A"); }
}
class B extends A {
    static void hello() { System.out.println("B"); }
}
public class Demo {
    public static void main(String[] args) {
        A obj = new B();
        obj.hello();  // in "A", vì static gọi theo kiểu reference
    }
}
```

### (b) Field hiding

* Fields không tham gia đa hình.
* Nếu subclass khai báo lại field, nó **che (hide)** field của superclass.

```java
class Parent {
    String name = "Parent";
}
class Child extends Parent {
    String name = "Child";
}
public class Demo {
    public static void main(String[] args) {
        Parent p = new Child();
        System.out.println(p.name); // "Parent", không đa hình
    }
}
```

### (c) Method hiding (tương tự static method)

---

# 5. Bài tập

👉 Viết hàm xử lý danh sách `Shape` mà **không dùng `instanceof` hay `switch`** → tận dụng **polymorphism**:

```java
import java.util.*;

interface Shape {
    double area();
}

class Circle implements Shape {
    double r;
    Circle(double r) { this.r = r; }
    public double area() { return Math.PI * r * r; }
}

class Square implements Shape {
    double s;
    Square(double s) { this.s = s; }
    public double area() { return s * s; }
}

public class PolymorphismDemo {
    static void printAreas(List<Shape> shapes) {
        for (Shape sh : shapes) {
            System.out.println("Area = " + sh.area()); 
            // không cần instanceof, tự động gọi method phù hợp
        }
    }

    public static void main(String[] args) {
        List<Shape> list = Arrays.asList(
            new Circle(3),
            new Square(4)
        );
        printAreas(list);
    }
}
```

---

✅ **Tóm tắt:**

* Compile-time polymorphism = **overloading** (biên dịch quyết định).
* Run-time polymorphism = **overriding** (JVM quyết định qua vtable).
* Static methods & fields **không đa hình**.
* Nên thiết kế API dựa trên polymorphism để tránh `instanceof`/`switch`.

---
Rồi 👍 mình sẽ tổng hợp cho bạn **Overloading vs Overriding trong Java** theo đúng các yêu cầu: bảng quy tắc, access modifier, return type, ví dụ đúng/sai + error, pitfalls, và một bài tập test case để bạn “chứng minh” quy tắc.

---

# 1. So sánh tổng quan

| Tiêu chí                 | **Overloading (nạp chồng)**                             | **Overriding (ghi đè)**                                                  |
| ------------------------ | ------------------------------------------------------- | ------------------------------------------------------------------------ |
| **Chữ ký phương thức**   | Cùng tên, **khác tham số** (số lượng hoặc kiểu)         | Cùng tên, cùng tham số                                                   |
| **Return type**          | Có thể khác, nhưng **không dùng để phân biệt overload** | Phải giống hoặc **covariant** (kiểu con của return type gốc)             |
| **Access modifier**      | Có thể khác tùy ý, không ảnh hưởng phân giải            | Không được **thu hẹp** (VD: `public` → `protected` ❌)                    |
| **Static methods**       | Có thể overload bình thường                             | Không override được → chỉ **method hiding**                              |
| **Exceptions**           | Có thể khai báo checked exception khác nhau             | Không được ném checked exception “rộng hơn” (chỉ cùng loại hoặc hẹp hơn) |
| **Varargs / Autoboxing** | Có thể tạo overload khác nhau (gây ambiguous)           | Không liên quan                                                          |
| **Thời điểm phân giải**  | Compile-time (early binding)                            | Runtime (late binding, vtable)                                           |

---

# 2. Access modifier chi tiết

### Overloading

```java
class Demo {
    public void f(int x) {}
    private void f(String s) {} // OK (không ảnh hưởng)
}
```

### Overriding

```java
class Parent {
    public void g() {}
}

class Child extends Parent {
    @Override
    protected void g() {} // ❌ Compile error: attempting to assign weaker access privileges
}
```

---

# 3. Return type chi tiết

### Overloading

```java
class Demo {
    int calc(int x) { return x; }
    double calc(double x) { return x; } // OK
    // int calc(int x) vs String calc(int x) ❌ không hợp lệ
}
```

> Lỗi: *“method calc(int) is already defined in class Demo”*

### Overriding

```java
class Parent {
    Object getData() { return "parent"; }
}

class Child extends Parent {
    @Override
    String getData() { return "child"; } // OK (covariant return)
}
```

---

# 4. Ví dụ đúng & sai

✅ Đúng:

```java
class Parent {
    void show(int x) { System.out.println("Parent int"); }
}

class Child extends Parent {
    @Override
    void show(int x) { System.out.println("Child int"); } // override
    void show(String s) { System.out.println("Child String"); } // overload
}
```

❌ Sai (return type conflict):

```java
class Demo {
    int test(int x) { return x; }
    String test(int x) { return "x"; } // Compile error
}
```

❌ Sai (narrowing access):

```java
class Parent {
    public void run() {}
}
class Child extends Parent {
    @Override
    void run() {} // ❌ error: attempting to assign weaker access privileges
}
```

---

# 5. Pitfalls

* **Ambiguous overload**

```java
void print(int x, long y) {}
void print(long x, int y) {}

print(1, 1); // ❌ error: reference to print is ambiguous
```

* **Autoboxing chọn nhầm overload**

```java
void f(Integer x) { System.out.println("Integer"); }
void f(long x) { System.out.println("long"); }

f(5); // in "long" (autoboxing < primitive widening rule)
```

* **Static method không override**

```java
class A { static void hello() { System.out.println("A"); } }
class B extends A { static void hello() { System.out.println("B"); } }

A a = new B();
a.hello(); // in "A", vì static chọn theo kiểu reference
```

---

# 6. Bài tập: Bộ test chứng minh quy tắc

👉 Viết class test sau, chạy để quan sát kết quả & compile error:

```java
class Parent {
    public Number calc(int x) { return x; }
    public static void hello() { System.out.println("Hello Parent"); }
}

class Child extends Parent {
    // Override: hợp lệ (covariant return)
    @Override
    public Integer calc(int x) { return x * 2; }

    // Method hiding (không override)
    public static void hello() { System.out.println("Hello Child"); }

    // Overload
    public double calc(double x) { return x * 1.5; }
}

public class TestOverloadOverride {
    public static void main(String[] args) {
        Parent p = new Child();

        // Runtime dispatch
        System.out.println(p.calc(10)); // gọi Child.calc(int) → 20

        // Compile-time overload
        Child c = new Child();
        System.out.println(c.calc(10.5)); // double version

        // Static method hiding
        p.hello(); // "Hello Parent"
        c.hello(); // "Hello Child"

        // Ambiguous case (bỏ comment để thấy lỗi)
        /*
        class Ambiguity {
            void f(int a, long b) {}
            void f(long a, int b) {}
        }
        new Ambiguity().f(1,1); // ❌ compile error
        */
    }
}
```

---

✅ **Tóm tắt**:

* Overloading: compile-time, khác tham số, return type không phân biệt, access modifier không hạn chế.
* Overriding: runtime, cùng signature, return type phải cùng/covariant, không thu hẹp access modifier, static chỉ hiding.
* Pitfalls: ambiguous overload, autoboxing, static methods, field hiding.

---
