# Nền tảng Lập trình Hướng Đối tượng (OOP)

OOP là một mô hình lập trình giúp chúng ta tổ chức code xoay quanh **"đối tượng"** thay vì **"hành động"**. Nó giúp code dễ quản lý, tái sử dụng và mở rộng hơn.

## Class và Object

### Khái niệm

Đây là cặp khái niệm nền tảng nhất của OOP.

* **Class (Lớp):** Là một bản thiết kế hoặc một khuôn mẫu. Nó định nghĩa các đặc điểm (thuộc tính) và hành vi (phương thức) mà các đối tượng thuộc lớp đó sẽ có. Bản thân Class không làm được gì cả, nó chỉ là một bản mô tả.
    * **Ví dụ đời thực:** Bản thiết kế của một chiếc xe hơi, định nghĩa rằng xe sẽ có màu sắc, thương hiệu, 4 bánh xe, và có khả năng chạy, dừng, tăng tốc.

* **Object (Đối tượng):** Là một thể hiện (instance) cụ thể của một Class. Nó được tạo ra từ bản thiết kế của Class đó. Mỗi đối tượng có một trạng thái riêng (giá trị của các thuộc tính) và tồn tại độc lập với các đối tượng khác.
    * **Ví dụ đời thực:** Chiếc VinFast Lux A2.0 màu đỏ của bạn, hay chiếc Toyota Vios màu trắng của tôi. Cả hai đều là những chiếc xe cụ thể, được sản xuất từ bản thiết kế "xe hơi".

### Thực hành

Hãy tạo lớp `Car` và các đối tượng từ nó.

**Java**

```java
// Car.java - Bản thiết kế
public class Car {
    String brand;
    String color;
    int currentSpeed;

    // Các phương thức sẽ được thêm sau...

    void displayInfo() {
        System.out.println("Car Info: Brand=" + brand + ", Color=" + color + ", Speed=" + currentSpeed + "km/h");
    }
}
````

````java
// Main.java - Nơi tạo và sử dụng đối tượng
public class Main {
    public static void main(String[] args) {
        // Tạo đối tượng Car thứ nhất - một thể hiện cụ thể
        Car vinfastCar = new Car();
        vinfastCar.brand = "VinFast";
        vinfastCar.color = "Red";
        vinfastCar.currentSpeed = 0;

        // Tạo đối tượng Car thứ hai - một thể hiện hoàn toàn riêng biệt
        Car toyotaCar = new Car();
        toyotaCar.brand = "Toyota";
        toyotaCar.color = "White";
        toyotaCar.currentSpeed = 20;

        System.out.println("Thông tin hai chiếc xe:");
        vinfastCar.displayInfo();
        toyotaCar.displayInfo();
    }
}
````

-----

## Thuộc tính (Attribute) và Phương thức (Method)

### Khái niệm

  * **Attribute (Thuộc tính):** Còn gọi là *field* hay *instance variable*. Chúng là các biến được khai báo bên trong một lớp, đại diện cho trạng thái hay đặc điểm của một đối tượng. Mỗi đối tượng sẽ có một bản sao riêng của các thuộc tính này.

      * **Ví dụ:** `brand`, `color`, `currentSpeed` của lớp `Car`.

  * **Method (Phương thức):** Là các hàm được định nghĩa bên trong một lớp, đại diện cho hành vi hay khả năng của một đối tượng. Phương thức thường dùng để thao tác trên các thuộc tính của chính đối tượng đó.

      * **Ví dụ:** Một chiếc xe có thể `start()`, `accelerate()`, `stop()`.

### Thực hành

Hãy thêm hành vi vào lớp `Car` của chúng ta.

**Java**

````java
// Car.java
public class Car {
    String brand;
    String color;
    int currentSpeed = 0; // Trạng thái

    // Hành vi
    void start() {
        System.out.println(brand + " is starting...");
    }

    // Hành vi làm thay đổi trạng thái
    void accelerate(int amount) {
        currentSpeed += amount;
        System.out.println("Accelerating. Current speed: " + currentSpeed + " km/h");
    }

    void stop() {
        currentSpeed = 0;
        System.out.println(brand + " has stopped.");
    }

    void displayInfo() {
        System.out.println("Car Info: Brand=" + brand + ", Color=" + color + ", Speed=" + currentSpeed + "km/h");
    }
}
````

````java
// Main.java
public class Main {
    public static void main(String[] args) {
        Car myCar = new Car();
        myCar.brand = "Mercedes";
        myCar.color = "Black";

        myCar.displayInfo(); // Speed = 0
        myCar.start();
        myCar.accelerate(50); // Speed = 50
        myCar.displayInfo();
        myCar.accelerate(30); // Speed = 80
        myCar.stop();
        myCar.displayInfo(); // Speed = 0
    }
}
````

-----

## Constructor (Phương thức khởi tạo)

### Khái niệm và Mục đích

**Constructor** là một "phương thức" đặc biệt được tự động gọi khi bạn tạo một đối tượng mới bằng từ khóa `new`.

  * **Mục đích chính:** Để khởi tạo trạng thái ban đầu cho đối tượng, đảm bảo đối tượng ở một trạng thái hợp lệ ngay khi vừa được sinh ra.
  * **Có phải là phương thức không?** Rất giống nhưng có 2 khác biệt chính:
    1.  Tên của constructor phải trùng chính xác với tên lớp.
    2.  Constructor không có kiểu trả về, kể cả `void`.

### Default Constructor

Nếu bạn không tự viết bất kỳ constructor nào trong lớp của mình, Java sẽ tự động cung cấp một **constructor mặc định (default constructor)**. Constructor này không có tham số và phần thân của nó rỗng. Đó là lý do tại sao code `new Car()` ở các ví dụ trên chạy được.

> **Lưu ý:** Ngay khi bạn tự định nghĩa dù chỉ một constructor, Java sẽ không cung cấp constructor mặc định nữa.

### Thực hành Constructor có tham số

Sẽ tiện lợi hơn nhiều nếu chúng ta có thể cung cấp `brand` và `color` ngay khi tạo xe.

**Java**

````java
// Car.java
public class Car {
    String brand;
    String color;
    int currentSpeed;

    // Constructor có tham số
    public Car(String initialBrand, String initialColor) {
        System.out.println("Constructor with parameters is called!");
        // 'this' được dùng để phân biệt thuộc tính của đối tượng (this.brand)
        // với tham số của constructor (initialBrand)
        this.brand = initialBrand;
        this.color = initialColor;
        this.currentSpeed = 0; // Khởi tạo trạng thái ban đầu
    }

    void displayInfo() {
        System.out.println("Car Info: Brand=" + brand + ", Color=" + color + ", Speed=" + currentSpeed + "km/h");
    }
}
````

````java
// Main.java
public class Main {
    public static void main(String[] args) {
        // Bây giờ, chúng ta PHẢI cung cấp brand và color khi tạo đối tượng
        Car porscheCar = new Car("Porsche", "Yellow");
        porscheCar.displayInfo();

        // Dòng này sẽ lỗi vì không còn constructor mặc định Car() nữa
        // Car anotherCar = new Car();
    }
}
````

-----

## Nạp chồng Constructor (Constructor Overloading)

### Khái niệm

**Nạp chồng (Overloading)** là khả năng định nghĩa nhiều phương thức (hoặc constructor) có cùng tên trong cùng một lớp, nhưng chúng phải khác nhau về danh sách tham số (khác về số lượng tham số hoặc kiểu dữ liệu của tham số).

Điều này cho phép chúng ta có nhiều cách linh hoạt để tạo ra một đối tượng.

### Thực hành

Hãy cung cấp nhiều cách để tạo một chiếc `Car`.

**Java**

````java
// Car.java
public class Car {
    String brand;
    String color;
    int currentSpeed;

    // 1. Constructor không tham số (No-arg constructor)
    // Tự định nghĩa lại để có thể tạo xe mà không cần thông tin ban đầu
    public Car() {
        this.brand = "Unknown";
        this.color = "White";
        this.currentSpeed = 0;
    }

    // 2. Constructor chỉ nhận brand
    public Car(String brand) {
        this.brand = brand;
        this.color = "Black"; // Gán màu mặc định
        this.currentSpeed = 0;
    }

    // 3. Constructor nhận cả brand và color
    public Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
        this.currentSpeed = 0;
    }
    // ... các phương thức khác
}
````

````java
// Main.java
public class Main {
    public static void main(String[] args) {
        Car car1 = new Car(); // Gọi constructor 1
        Car car2 = new Car("Ford"); // Gọi constructor 2
        Car car3 = new Car("Lamborghini", "Orange"); // Gọi constructor 3

        System.out.println("Car 1: " + car1.brand + ", " + car1.color);
        System.out.println("Car 2: " + car2.brand + ", " + car2.color);
        System.out.println("Car 3: " + car3.brand + ", " + car3.color);
    }
}
````

-----

## Access Modifiers (Phạm vi truy cập)

### Khái niệm

**Access Modifiers** là các từ khóa dùng để thiết lập khả năng truy cập (khả năng nhìn thấy và sử dụng) của các lớp, thuộc tính, và phương thức.

Đây là công cụ chính để thực hiện nguyên lý **đóng gói (encapsulation)** - một trong 4 trụ cột của OOP. Đóng gói có nghĩa là che giấu đi những chi tiết triển khai bên trong của một đối tượng và chỉ cung cấp một giao diện công khai (public) để tương tác với nó. Điều này giúp bảo vệ dữ liệu khỏi bị thay đổi một cách không hợp lệ.

### Phân loại

| Modifier                | Cùng Lớp (Class) | Cùng Gói (Package) | Lớp con (Subclass) | Ngoài Gói | Mô tả                                                                     |
| ----------------------- | :--------------: | :----------------: | :----------------: | :-------: | ------------------------------------------------------------------------- |
| **`public`** |        ✅        |         ✅         |         ✅         |    ✅     | Công khai: Có thể truy cập từ bất cứ đâu.                                  |
| **`protected`** |        ✅        |         ✅         |         ✅         |    ❌     | Được bảo vệ: Có thể truy cập trong cùng package và bởi các lớp con.       |
| **`default`** (không ghi gì) |        ✅        |         ✅         |         ❌         |    ❌     | Mặc định: Chỉ có thể truy cập bên trong cùng một package.                  |
| **`private`** |        ✅        |         ❌         |         ❌         |    ❌     | Riêng tư: Chỉ có thể truy cập bên trong chính lớp đó. Mức bảo vệ cao nhất. |

### Thực hành

Hãy áp dụng Access Modifiers vào lớp `Car` để đóng gói dữ liệu.

> **Quy tắc chung:**
>
>   * Các thuộc tính (trạng thái) nên là `private` để bảo vệ chúng.
>   * Các phương thức (hành vi) mà bạn muốn thế giới bên ngoài sử dụng nên là `public`.

**Java**

````java
// Car.java - Phiên bản được đóng gói
public class Car {
    // 1. Các thuộc tính bây giờ là private
    // Không ai từ bên ngoài lớp Car có thể truy cập trực tiếp
    // ví dụ: myCar.brand = "..." sẽ gây lỗi
    private String brand;
    private String color;
    private int currentSpeed;

    public Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
        this.currentSpeed = 0;
    }

    // 2. Cung cấp các phương thức "Getter" public để đọc dữ liệu
    public String getBrand() {
        return this.brand;
    }

    public String getColor() {
        return this.color;
    }

    public int getCurrentSpeed() {
        return this.currentSpeed;
    }

    // 3. Cung cấp các phương thức "Setter" public để thay đổi dữ liệu một cách có kiểm soát
    public void setColor(String color) {
        // Có thể thêm logic kiểm tra ở đây nếu cần
        this.color = color;
    }

    // Các phương thức hành vi public
    public void accelerate(int amount) {
        if (amount > 0) {
            this.currentSpeed += amount;
            System.out.println("Current speed: " + this.currentSpeed + " km/h");
        }
    }

    public void stop() {
        this.currentSpeed = 0;
        System.out.println("Car has stopped.");
    }
}
````

````java
// Main.java
public class Main {
    public static void main(String[] args) {
        Car mySecureCar = new Car("BMW", "Blue");

        // Lấy thông tin thông qua các phương thức public "getter"
        System.out.println("Brand: " + mySecureCar.getBrand());

        // Thay đổi thông tin thông qua phương thức public "setter"
        mySecureCar.setColor("Silver");
        System.out.println("New color: " + mySecureCar.getColor());

        // Dòng này sẽ gây lỗi biên dịch vì 'currentSpeed' là private
        // mySecureCar.currentSpeed = 1000;

        // Tương tác với đối tượng thông qua giao diện public của nó
        mySecureCar.accelerate(80);
        System.out.println("Speed from getter: " + mySecureCar.getCurrentSpeed());
    }
}
````