# The Liskov Substitution Principle

The Liskov Substitution principle was introduced by Barbara Liskov in her conference keynot "Data abstraction" in 1987. A few years later, she published a paper with Jeanette Wing in which they defined the principle as:

> Let Φ(x) be a property provable about objects x of type T. Then Φ(y) should be true for objects y of type S where S is a subtype of T.

* If class B is a subclass of class A, we should be able to use B instead of A without unexpected behavior.

* Violations occur when a subclass changes behavior unexpectedly or removes functionality from the parent class.

Ok, let's be honest. Such a scientific definition might be necessary, but it doesn't help a lot in our daily work as software developers. So, what does it mean for our code?

1. Objects of a superclass should be able to be replaced with objects of a subclass without affecting the program.
2. Objects of subclass should be able to access the all the methods and properties of the superclass.

Let's explore two example:

Example 1:
Here, Green is a base super class of Blue. When a new object of Blue will be assigned in object of Super class, getColor() of Green will be replaced by getColor() of Blue according to inheritance rules of OOP. In this situation, we are expecting "Green" color from Green class but we are getting "Blue" color from Green color. So the design of class is violation the LSP.

public class Green {
  public void getcolor() {
    System.out.println("Green");
  }
}

public class Blue extends Green {
  public void getColor() {
    System.out.println("Blue");
  }
}

public class Main {
  public static void main(String[] args) {
    Green green = new Blue();
    green.getColor();
  }
}

In the main method, green object is asking for color but it's getting "Blue" color. How to solve this issue. See the solution.

public interface IColor {
  public void getColor();
}

public class Green implements IColor {
  public void getColor() {
    System.out.println("Green");
  }
}

public class Blue implements IColor {
  public void getColor() {
    System.out.println("Blue");
  }
}

public class Main{
  public static void main(String[] args) {
    IColor color = new Blue();
    color.getColor(); // Output: Blue
  }
}

Example 2:
Here, "Fly()" and "walk()" are the actions of Bird class. Penguin inherits the Bird class but Penguin can not fly. As a child class Penguin can not use the fly. So design of class can not satisfy the LSP rules.

public interface Bird {
  public void fly();
  public void walk();
}

public class Parrot implements Bird {
  public void fly() {
    // to do
  }
  public void walk() {
    // to do
  }
}

public class Penguin implements Bird {
  public void fly() {
    // to do
  }
  public void walk() {
    // to do
  }
  // It's breaking the Liskov's substitute principle. Penguin can not fly.
}

Solution:

public interface Bird {
  // to do;
}

public interface FlyingBird extends Bird {
  public void fly();
}

public interface WalkingBird extends Bird {
  public void walk();
}

public class Parrot implements FlyingBird, WalkingBird {
  public void fly() {
    // to do
  }
  public void walk() {
    // to do
  }
  public class Penguin implements WalkingBird {
    public void walk() {
      // to do
    }
  }
}

Example 3: Violating LSP (Bad Example)
let's consider a scenario involving a simple hierarchy of shapes. We have a base class Rectangle and a derived class Square.

public class Rectangle {
  protected int width;
  protected in height;

  public void setWidth(int width) {
    this.width = width;
  }
  public void setHeight(int height) {
    this.height = height;
  }
  public int getArea() {
    return width * height;
  }
}

public class Square extends Rectangle {
  @Override
  public void setWidth(int width) {
    this.width = width;
    this.height = width;
  }

  @Override
  public void setHeight(int height) {
    this.height = height;
    this.width = height;
  }
}

Here, Square is a subclass of Rectangle, but it violates LSP because it changes the behavior of the setWidth and setHeight methods. In a square the width and height are always the same, so when you set the width or height, it automatically changes the other dimension. This behavior may be unexpected if someone tries to use a Square where a Rectangle is expected.

Example of a violation in Practice:
public void resizeRectangle(Rectangle rectangle) {
  rectangle.setWidth(5);
  rectangle.setHeight(10);

  System.out.println("Area: " + rectangle.getArea());
}

If we pass a Square to the resizeRectangle method, the result will not be as expected. The width and height will bot be set to the same value, which violates the expected behavior of a Rectangle. This breaks the substitution property, violating LSP.

Applying LSP Correctly (Good Example)
The adhere to LSP, we neet to reconsider the design, Instead of making Square a subclass of Rectangle, we should either:

* Keep them separate without an inheritance relationship.
* Use interfaces to define a common behavior, like Shape.

Here's a solution using an interface:

public interface Shape {
  int getArea();
}

public class Rectangle implements Shape {
  private int width;
  private int height;

  public Rectangle(int width, int height) {
    this.width = width;
    this.height = height;
  }

  @Override
  public int getArea() {
    return width * height;
  }
}

public class Square implements Shape {
  private int side;
  public Square(int side) {
    this.side = side;
  }
  @Override
  public int getArea() {
    return side * side;
  }
}

In this refactored version:
* Both Rectangle and Square implement the Shape interface, defining their behavior independently.
* The substitution issue is resolve because both classes behave correctly when getArea() is called.


E-Commerce App Example of Spring Boot.

Imaging an E-commerce system where we have Admin Users and Regular Users.

Bad practice:
Here, RegularUser overrides hasAdminAccess() incorrectly, violation LSP.

* Base Class (User)
public class User {
  public boolean hasAdminAccess() {
    return true;
  }
}

* Subclass (AdminUser) - Follows Liskov Substitute Principle
public class AdminUser extends User {
  @Override
  public boolean hasAdminAccess() {
    return true;
  }
}

* Subclass (RegularUser - Violates Liskove Substitute Principle)
public class RegularUser extends User {
  @Override
  public boolean hasAdminAccess() {
    throw new UnsupportedOperationException("Regular users cannot access admin features!");
  }
}

* Problem:
If we use a RegularUser in a method expection a User, the program breaks.
public void checkAccess(User user) {
  if(user.hasAdminAccess()) {
    System.out.println("Access granted to admin panel");
  } else {
    System.out.println("Access denied");
  }
}

* Good Practice (Applying Liskov Substitute Principle)
Step 1: Define a User Interface
public interface User {
  boolean hasAdminAccess();
}

Step 2: Implement Separate Classes
public class AdminUser implements User {
  @Override
  public boolean hasAdminAccess() {
    return true;
  }
}

public class Regularuser implements user {
  @Override
  public boolean hasAdminAccess() {
    return false;
  }
}

Step 3: Use Code Without Breaking Liskov Susbtitute Principle
public void checkAccess(User user) {
  if(user.hasAdminAccess()) {
    System.out.println("Access granted to admin panel");
  } else {
    System.out.pirntln("Access denied");
  }
}
* Now, any User can be passed without unexpected exceptions.

Example 2: Payment Processing in an E-commerce App
A CashPayment class should not override onlie payment methods because it does not support them.

* Bad practice (Violating Liskov Design Principle)
Here, CashPayment overrides processRefund() incorrectly.

* Base Class (Online Payment)
public class OnlinePayment {
  public void processPayment(double amount) {
    System.out.pirntln("Processing online payment: " + amount);
  }
  public void processRefund(double amount) {
    System.out.println("Processing refund: " + amount);
  }
}

* Subclass (CashPayment) - Violates Liskov Substitue Principle
public class CashPaypment extends OnlinePayment {
  @Override
  public void processRefund(double amount) {
    throw new UnsupportedOperationException("Cash payments cannot be refunded!");
  }
}

* Problem
If we use CashPayment in a method expecting OnlinePayment, it breaks.
public void issueRefund(OnlinePayment payment, double amount) {
  payment.processRefund(amount);
}
*Throws an exception for CashPayment, violation Liskov Substitute Principle.

Good Practice (Applying Liskov substitution Principle)

We separate concerns by using interfaces for different payment types
Step 1: Define Payment interfaces
public interface Payment {
  void processPayment(double amount);
}

public interface RefundablePayment extends payment {
  void processRefund(double amount);
}

Step 2: Implement Separate Classes
public class CreditCardPayment implements RefundablePayment {
  @Override
  public void processPayment(double amount) {
    System.out.pirntln("Processing Credit Card payment: " + amount);
  }

  @Override
  public void processRefund(double amount) {
    System.out.pirntln("Processing refund: " + amount);
  }
}

public class CashPayment implements Payment {
  @Override
  public void processPayment(double amount) {
    System.out.pirntln("Processing cash payment: " + amount);
  }
}

Step 3: Use Code Without Breaking LSP
public void issueRefund(RefundablePayment payment, double amount) {
  payment.processRefund(amount);
}
* Now, CashPayment cannot be used where refunds are expected, avoiding runtime errors.

Example 3: Product Variants in an E-Commerce App
Bad Practice:

If DigitalProduct tries to override getShippingCost(), it violates LSP
Base Class(Product)
public class Product {
  public double getShippingCost() {
    return 10.0;
  }
}

Subclass (DigitalProduct) - Violates LSP
public class DigitalProduct extends Product {
  @Override
  public double getShippingCost() {
    throw new UnsupportedOperationException("Digital products have no shipping cost");
  }
}

* Problem
If we call getShippingCost() on a DigitalProduct, the program breaks.

public void printShippingCost(Product product) {
  System.out.pirntln("Shipping Cost: " + product.getShippingCost());
}
* Throws an exception for DigitalProduct, violation LSP.

Good Practice (Applying LSP)
We separate physical and digital products into different classes.

Step 1: Define Product interfaces

public interface Product {
  String getName();
}

public interface ShippableProduct extends Product {
  double getShippingCost();
}

Step 2: Implement Separate Classes
public class PhysicalProduct implements ShippableProduct {
  private String name;

  public PhysicalProduct(String name) {
    this.name = name;
  }

  @Override
  public String getName() {
    return name;
  }

  @Override
  public double getShippingCost() {
    return 10.0;
  }
}

public class DigitalProduct implements Product {
  private String name;

  public DigitalProduct(String name) {
    this.name = name;
  }

  @Override
  public String getName() {
    return name;
  }
}

Step 3: Use Code Without Breaking LSP
public void printShippingCost(ShippableProduct product) {
  System.out.println("Shipping Cost: " + product.getShippingCost());
}

Bad Practice:
Subclasses overrides methods incorrectly
Throws runtime exceptions
Required additional if checks


Good Practice LSP Applied
Uses proper abstraction
Prevents invalid usage
Uses correct interfaces.
