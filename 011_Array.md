# 📌 Phần 1: Mảng và Quản lý Bộ nhớ trong Java

## 1.1. Bản chất của Mảng trong Java

### 🔹 Vì sao mảng được coi là **“đối tượng”?**

Trong Java:

* Bất kỳ thứ gì được tạo bằng `new` đều là **đối tượng**.
* Mảng (`int[]`, `String[]`, `Student[]`…) là một loại **đối tượng đặc biệt** kế thừa từ lớp `Object`.
* Do đó, nó có các thuộc tính (ví dụ `length`) và có thể được truyền cho phương thức như một tham chiếu.

👉 Mảng **không phải là biến nguyên thủy** mà là **object chứa nhiều phần tử**.

### 🔹 Đặc điểm quan trọng

1. **Kích thước cố định**: Sau khi tạo mảng, số phần tử không thể thay đổi.
   → Vì JVM cần biết chính xác kích thước để cấp phát bộ nhớ liên tục trong **Heap**.
   → Muốn thay đổi kích thước, ta phải tạo mảng mới.

2. **Kiểu dữ liệu đồng nhất**: Tất cả phần tử trong mảng phải cùng kiểu (`int[]` chỉ chứa int).
   → Giúp JVM quản lý và truy cập nhanh hơn (tính toán offset).

3. **Lưu trữ trên Heap**: Mảng được tạo bằng `new` → nằm trên Heap. Biến tham chiếu mảng nằm trên Stack.

---

## 1.2. Thực hành với Mảng

### 🔹 Mảng Nguyên thủy

```java
public class ArrayDemo {
    public static void main(String[] args) {
        // Khai báo và cấp phát bộ nhớ cho mảng 5 phần tử int
        int[] numbers = new int[5];  

        // Gán giá trị
        numbers[0] = 10;
        numbers[1] = 20;
        numbers[2] = 30;
        numbers[3] = 40;
        numbers[4] = 50;

        // Duyệt mảng
        for (int i = 0; i < numbers.length; i++) {
            System.out.println("numbers[" + i + "] = " + numbers[i]);
        }
    }
}
```

👉 Ban đầu các phần tử `int` trong mảng mặc định = `0`.

---

### 🔹 Mảng Đối tượng

```java
class Student {
    String name;
    int age;

    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    void display() {
        System.out.println(name + " - " + age);
    }
}

public class StudentArrayDemo {
    public static void main(String[] args) {
        // Khởi tạo mảng 3 phần tử
        Student[] students = new Student[3];

        // Ban đầu mỗi phần tử là null
        System.out.println(students[0]); // null

        // Phải khởi tạo từng đối tượng
        students[0] = new Student("An", 20);
        students[1] = new Student("Bình", 21);
        students[2] = new Student("Chi", 22);

        // Duyệt mảng
        for (Student s : students) {
            s.display();
        }
    }
}
```

👉 Rất quan trọng: Mảng đối tượng chỉ chứa **tham chiếu**, mỗi phần tử phải được `new` riêng.

---

## 1.3. Mô hình Bộ nhớ Java

### 🔹 Tổng quan

* JVM quản lý bộ nhớ gồm nhiều vùng, quan trọng nhất: **Stack** và **Heap**.

### 🔹 Bảng so sánh Stack vs Heap

| Tiêu chí         | **Stack**                         | **Heap**                              |
| ---------------- | --------------------------------- | ------------------------------------- |
| Chứa gì?         | Biến cục bộ, tham chiếu đối tượng | Đối tượng, mảng, dữ liệu thực         |
| Cấp phát/thu hồi | Tự động (LIFO) khi hàm vào/thoát  | Do **Garbage Collector (GC)** quản lý |
| Tốc độ           | Rất nhanh                         | Chậm hơn (do quản lý phức tạp)        |
| Kích thước       | Nhỏ hơn                           | Lớn hơn                               |
| Vòng đời dữ liệu | Theo scope (hết hàm sẽ mất)       | Tồn tại đến khi không còn tham chiếu  |

### 🔹 Minh họa code + sơ đồ

```java
class Car {
    String brand;
    Car(String brand) {
        this.brand = brand;
    }
}

public class MemoryDemo {
    public static void main(String[] args) {
        Car car1 = new Car("Toyota"); // car1 nằm ở Stack, object Car nằm ở Heap
        System.out.println(car1.brand);
    }
}
```

📌 Sơ đồ:

```
Stack:                 Heap:
car1 ───────────────►  [Car object: brand="Toyota"]
```

---

## 1.4. Rò rỉ bộ nhớ (Memory Leak)

### 🔹 Khái niệm

Memory Leak = Khi chương trình **không còn cần một đối tượng**, nhưng vẫn giữ **tham chiếu tới nó**, khiến GC **không dọn dẹp**.

### 🔹 Nguyên nhân phổ biến

* Giữ đối tượng trong **static collection**.
* Không giải phóng resource (File, Socket…).
* Listener/Callback không được remove.

### 🔹 Ví dụ

```java
import java.util.ArrayList;
import java.util.List;

public class MemoryLeakDemo {
    private static List<Object> cache = new ArrayList<>();

    public static void main(String[] args) {
        for (int i = 0; i < 1000000; i++) {
            // Đối tượng không còn dùng nhưng vẫn giữ tham chiếu trong cache
            cache.add(new byte[1024 * 1024]); 
        }
    }
}
```

👉 Cách khắc phục: Xóa tham chiếu khi không cần nữa

```java
cache.clear(); // hoặc gán null cho phần tử
```

---

# 📌 Phần 2: Nền tảng Lập trình Hướng Đối tượng (OOP)

## 2.1. Class và Object

### 🔹 Khái niệm

* **Class** = Bản thiết kế ngôi nhà.
* **Object** = Ngôi nhà cụ thể được xây từ bản thiết kế đó.

### 🔹 Thực hành

```java
class Car {
    String brand;
    String color;
    int currentSpeed;

    void start() {
        System.out.println(brand + " is starting...");
    }
}

public class OOPDemo {
    public static void main(String[] args) {
        Car vinfastCar = new Car();
        vinfastCar.brand = "VinFast";
        vinfastCar.color = "Red";

        Car toyotaCar = new Car();
        toyotaCar.brand = "Toyota";
        toyotaCar.color = "Blue";

        vinfastCar.start(); // VinFast is starting...
        toyotaCar.start();  // Toyota is starting...
    }
}
```

---

## 2.2. Thuộc tính và Phương thức

```java
class Car {
    private String brand;
    private String color;
    private int currentSpeed = 0;

    void start() {
        System.out.println(brand + " started.");
    }

    void accelerate(int value) {
        currentSpeed += value;
        System.out.println(brand + " accelerated. Speed = " + currentSpeed);
    }

    void stop() {
        currentSpeed = 0;
        System.out.println(brand + " stopped.");
    }

    // Setter để gán giá trị
    public void setInfo(String brand, String color) {
        this.brand = brand;
        this.color = color;
    }
}
```

---

## 2.3. Constructor

### 🔹 Đặc điểm

* Dùng để khởi tạo trạng thái ban đầu cho Object.
* Không có kiểu trả về, tên giống tên lớp.
* Nếu không khai báo, Java tự tạo **default constructor**.

### 🔹 Ví dụ Constructor

```java
class Car {
    String brand;
    String color;
    int currentSpeed;

    // Constructor mặc định
    Car() {
        this.brand = "Unknown";
        this.color = "White";
    }

    // Constructor có tham số
    Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
    }
}
```

---

## 2.4. Nạp chồng Constructor

```java
class Car {
    String brand;
    String color;

    // Constructor không tham số
    Car() {
        this("Unknown", "White");
    }

    // Constructor 1 tham số
    Car(String brand) {
        this(brand, "White");
    }

    // Constructor 2 tham số
    Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
    }
}
```

---

## 2.5. Access Modifiers

### 🔹 Bảng phạm vi truy cập

| Modifier                      | Trong cùng lớp | Cùng package | Lớp con (khác package) | Ngoài package |
| ----------------------------- | -------------- | ------------ | ---------------------- | ------------- |
| **public**                    | ✅              | ✅            | ✅                      | ✅             |
| **protected**                 | ✅              | ✅            | ✅                      | ❌             |
| **default** (package-private) | ✅              | ✅            | ❌                      | ❌             |
| **private**                   | ✅              | ❌            | ❌                      | ❌             |

### 🔹 Thực hành

```java
class Car {
    private String brand;   // Chỉ truy cập trong class
    public String color;    // Ai cũng thấy
    protected int year;     // Lớp con có thể dùng
    String model;           // default: chỉ trong package

    public Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
    }

    private void secretMethod() {
        System.out.println("This is private!");
    }
}
```

👉 Thuộc tính thường để **private**, cung cấp **getter/setter** để đảm bảo **Encapsulation**.

---
Rất hay 👌. Em đang tiến đúng nhịp: sau khi hiểu **Class, Object, Attribute, Method, Constructor, Access Modifier**, bây giờ bước tiếp theo là:

1. **Thực hành Access Modifiers** một cách đầy đủ và thực tế.
2. Hiểu và áp dụng **Inheritance (Kế thừa)** trong Java.

Anh sẽ đi chi tiết, kèm ví dụ code minh họa.

---

# 📌 1. Thực hành Access Modifiers

Java có 4 mức truy cập:

* **private**: chỉ dùng trong chính class đó.
* **default** (package-private): chỉ dùng trong cùng package.
* **protected**: dùng trong cùng package, và các lớp con (subclass) ở package khác.
* **public**: mọi nơi đều có thể dùng.

---

## 🔹 Ví dụ: Thực hành Access Modifiers

Tạo 2 file trong cùng package, và một file ở package khác.

### 📁 File `Car.java` (trong package `vehicles`)

```java
package vehicles;

public class Car {
    private String engineNumber;  // Chỉ Car mới truy cập
    String model;                 // default: chỉ trong package
    protected String brand;       // cùng package + subclass
    public String color;          // truy cập mọi nơi

    public Car(String brand, String color, String model, String engineNumber) {
        this.brand = brand;
        this.color = color;
        this.model = model;
        this.engineNumber = engineNumber;
    }

    // Getter cho private field
    public String getEngineNumber() {
        return engineNumber;
    }

    private void showSecret() {
        System.out.println("This is a private method in Car!");
    }

    void showDefault() {
        System.out.println("This is default method in Car!");
    }

    protected void showProtected() {
        System.out.println("This is protected method in Car!");
    }

    public void showPublic() {
        System.out.println("This is public method in Car!");
    }
}
```

---

### 📁 File `CarTest.java` (trong cùng package `vehicles`)

```java
package vehicles;

public class CarTest {
    public static void main(String[] args) {
        Car car = new Car("Toyota", "Red", "Vios", "EN123");

        // private: ❌ Không truy cập được
        // System.out.println(car.engineNumber);

        // default: ✅ trong cùng package
        System.out.println("Model: " + car.model);

        // protected: ✅ trong cùng package
        System.out.println("Brand: " + car.brand);

        // public: ✅ truy cập được
        System.out.println("Color: " + car.color);

        // Gọi các method
        // car.showSecret(); ❌ private
        car.showDefault();   // ✅ default
        car.showProtected(); // ✅ protected
        car.showPublic();    // ✅ public
    }
}
```

---

### 📁 File `ForeignTest.java` (trong package `test`)

```java
package test;

import vehicles.Car;

public class ForeignTest {
    public static void main(String[] args) {
        Car car = new Car("VinFast", "Blue", "LuxA", "EN456");

        // private: ❌
        // default: ❌ khác package
        // protected: ❌ khác package, không phải subclass
        // public: ✅
        System.out.println("Color: " + car.color);

        // Method
        // car.showSecret(); ❌
        // car.showDefault(); ❌
        // car.showProtected(); ❌
        car.showPublic(); // ✅
    }
}
```

👉 Kết luận:

* Trong cùng package → dùng được `default` và `protected`.
* Khác package → chỉ dùng `public`. `protected` chỉ có tác dụng nếu là **kế thừa**.

---

# 📌 2. Inheritance (Kế thừa)

## 🔹 Khái niệm

* **Inheritance** = một lớp (subclass/con) kế thừa từ một lớp khác (superclass/cha).
* Subclass sẽ **tái sử dụng thuộc tính + phương thức** của superclass.
* Dùng từ khóa `extends`.

👉 So sánh thực tế:

* **Class cha (Animal)** = “mẫu chung”.
* **Class con (Dog, Cat)** = “cụ thể hóa”, có thêm đặc điểm riêng.

---

## 🔹 Ví dụ: Kế thừa đơn giản

```java
// Lớp cha
class Vehicle {
    String brand;

    void start() {
        System.out.println(brand + " is starting...");
    }
}

// Lớp con kế thừa Vehicle
class Car extends Vehicle {
    int numberOfDoors;

    void showInfo() {
        System.out.println("Brand: " + brand + ", Doors: " + numberOfDoors);
    }
}

// Test
public class InheritanceDemo {
    public static void main(String[] args) {
        Car car = new Car();
        car.brand = "Toyota"; // Kế thừa từ Vehicle
        car.numberOfDoors = 4;
        
        car.start();      // Gọi method từ Vehicle
        car.showInfo();   // Gọi method của Car
    }
}
```

👉 Output:

```
Toyota is starting...
Brand: Toyota, Doors: 4
```

---

## 🔹 protected + Inheritance

```java
package vehicles;

public class Vehicle {
    protected String brand;

    protected void showBrand() {
        System.out.println("Brand: " + brand);
    }
}
```

```java
package test;

import vehicles.Vehicle;

public class Truck extends Vehicle {
    int loadCapacity;

    public Truck(String brand, int loadCapacity) {
        this.brand = brand; // ✅ vì brand là protected
        this.loadCapacity = loadCapacity;
    }

    public void showTruck() {
        showBrand(); // ✅ gọi method protected từ cha
        System.out.println("Load: " + loadCapacity + " tons");
    }
}
```

```java
package test;

public class InheritanceTest {
    public static void main(String[] args) {
        Truck truck = new Truck("Hino", 10);
        truck.showTruck();
    }
}
```

👉 Output:

```
Brand: Hino
Load: 10 tons
```

---

## 🔹 Quy tắc quan trọng khi kế thừa

1. **Một class chỉ kế thừa được 1 class cha** (Java chỉ hỗ trợ **single inheritance**, không đa kế thừa).
2. **Constructor không được kế thừa**, nhưng subclass có thể gọi constructor của superclass bằng `super()`.
3. Subclass có thể **ghi đè phương thức** (method overriding).

---

# 📌 3. Tổng kết

* Em đã nắm:
  ✅ Thực hành đầy đủ Access Modifiers (private, default, protected, public).
  ✅ Hiểu Inheritance: lớp con kế thừa thuộc tính/phương thức của lớp cha.
  ✅ Áp dụng `protected` trong kế thừa.
  ✅ Biết quy tắc kế thừa trong Java.

---

👉 Anh gợi ý: bước tiếp theo nên tìm hiểu về:

* **Method Overriding vs Overloading** (ghi đè và nạp chồng).
* **super** (gọi constructor cha, gọi method cha).
* **Polymorphism (đa hình)**.

---