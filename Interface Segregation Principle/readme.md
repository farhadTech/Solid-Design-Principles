# Interface Segregation Principle

> This principle states that clients should not be forces to depend on interfaces that they do not use.

This is referring to something knows as interface pollution. In other words we should avoid making large interfaces that contain many methods that are considered unrelated to each other.


** To determine whether you are breaking this rule or not consider the following:

Do your classes have empty method implementations?

Do your method implementations throw UnsupportedOperationException or something similar?

Do your method implementations return null or default/dummy values?

If so then you may be violating the rule.

Why use the Interface Segregation Principle?
Following the ISP helps reduce the number of potential side effects from additional code and also reduces the frequency of required changes by separating the code into independent parts.

Below is a simple example that does not adhere to ISP.
**a cold-blooded vertebrate animal of a class that comprises the frogs, toads, newts, salamanders, and caecilians. They are distinguished by having an aquatic gill-breathing larval stage followed (typically) by a terrestrial lung-breathing adult stage.**

public interface Amphibian {
  void swim();
  void walk();
  void jump();
  void breathUnderwater();
}

class Frog implements Amphibian {
  @Override
  public void swim() {
    System.out.println("Frog is swimming.");
  }
  @Override
  publiv void jump() {
    System.out.println("Frog is jumping.");
  }
  @Override
  public void breathUnderwater() {
    System.out.println("Frog can breath under water.");
  }
}

class salamander implements Amphibian {
  @Override
  public void swim() {
    System.out.println("Salamander is swimming.");
  }
  @Override
  public void walk() {
    System.out.println("Salamander is walking.");
  }
  @Override
  public void jump() {
    throw new UnsupportedOperationExceptiion("Salamander cannot jump.");
  }
  @Override
  public void breathUnderwater() {
    System.out.println("Salamander can breath under water.");
  }
}

public class Axolotl implements Amphibian {
  @Override
  public void swim() {
    System.out.println("Axoltol is swimming.");
  }
  @Override
  public void walk() {
    throw new UnsupportedOperationException("Exolotol cannot walk.");
  }
  @Override
  public void jump() {
    throw new UnsupportedOperationException("Exolton cannot jump.");
  }
  @Override
  public void breathUnderwater() {
    System.out.println("Exoltol can breathe under water.");
  }
}

**Problems in the example.**
1. Unnecessary method implementation.
2. Unsupported Operation.
3. Poor maintainability.

**Good Practice.**
public interface Swimmer {
  void swim();
}
public interface Walker {
  void walk();
}
public interface Jumper {
  void jump();
}
public interface UnderwaterBreather {
  void breathUnderwater();
}

public class Frog implements Swimmer, Jumper, UnderwaterBreather {
  @Override
  public void swim() {
    System.out.println("Frog is swimming.");
  }
  @Override
  public void jump() {
    System.out.println("Frog is jumping.");
  }
  @Override
  public void breathUnderwater() {
    System.out.println("Frog can breath under water.");
  }
}

public class Salamander implements Swimmer, Walker, UnderWaterBreather {
  @Override
  public void swim() {
    System.out.println("Salamander is swimming.");
  }
  @Override
  public void walk() {
    System.out.println("Salamander is walking.");
  }
  @Override
  public void breathUnderwater() {
    System.out.println("Salamander can breath under water.");
  }
}

public class Axolotl implements Swimmer, UnderwaterBreather {
  @Override
  public void swim() {
    System.out.println("Axoltol is swimming.");
  }
  @Override
  public void breathUnderwater() {
    System.out.println("Exoltol can breathe under water.");
  }
}

Another example
Bad practice:
We have an interface for a washing machine. This interface has two methods : washClothes() and dryClothes()

interface WashingMachine {
  void washClothes();
  void dryClothes();
}

class ComboWashingMachine implements WashingMachine {
  @Override
  public void washClothes() {
    System.out.println("Clothes washed in a combo washing machine");
  }
  @Override
  public void dryClothes() {
    System.out.println("Clothes dried in a combo washing machine");
  }
}

class SimpleWashingMachine implements WashingMachine {
  @Override
  public void washClothes() {
    System.out.println("Clothes washed in a simple washing machine");
  }
  @Override
  public void dryClothes() {
    throw new UnsupportedOperationException("This method doesn't apply to a simple washing machine");
  }
}

Using the interface on a simple washing machine means that the method dryClothes() will be unusable and unncessary. This is a violation of the ISP and we should refactor our interface so we don't have any empty method implementations or return any dummy values.

interface Washer {
  void washClothes();
}

interface Dryer {
  void dryClothes();
}

class ComboWashingMachine implements Washer, Dryer {
  @Override
  public void washClothes() {
    System.out.println("Clothes washed in a combo washing machine");
  }
  @Override
  public void dryClothes() {
    System.out.println("Clothes dried in a combo washing machine");
  }
}

class SimpleWashingMachine implements Washer {
  @Override
  public void washClothes() {
    System.out.println("Clothes washed in a simple washing machine");
  }
}

Difference Between Single Responsibility Principle and Interface Segregation Principle:

1. Single Responsibility Principle (SRP)
Definition: A class should have only one reason to change. It should do just one thing.

Violation SRP (One class doing too much)

class FileManager {
  public void readFile(String filePath) {
    System.out.println("Reading file: " + filePath);
  }
  public void writeFile(String filePath, String content) {
    System.out.println("Writing to file: " + filePath);
  }
  public void printFile(String filePath) {
    System.out.println("Printing file: " + filePath);
  }
}

Problem: The FileManager class handles reading, writing, and printing, which are separate concerns.

Following SRP (Separate Responsibilities)
class FileReader {
  public void readFile(String filePath) {
    System.out.println("Reading file: " + filePath);
  }
}

class FileWriter {
  public void writeFile(String filePath, String content) {
    System.out.println("Writing to file: " + filePath);
  }
}

class filePrinter {
  public void printFile(String filePath) {
    System.out.println("Printing file: " + filePath);
  }
}

2. Interface Segregation Principle (ISP)
Definition: A class should be be forced to implement methods it does not use. Instead of creating one large interface, break it into smaller, more specific interfaces.

Violation of ISP (One large Interface)
interface Worker {
  void work();
  void eat();
  void sleep();
  void charge();
}

Problem:
* A robot can work but does not eat or sleep.
* A human does all three.
* Robots should not be forced to implement eat() and sleep()

Following ISP (Smaller, More Focused Interfaces)
interface Workable {
  void work();
}
interface Eatable {
  void eat();
}
interface Sleepable {
  void sleep();
}

class Human implements Workable, Eatable, Sleepable {
  public void work() {
    System.out.println("Human is working.");
  }
  public void eat() {
    System.out.println("Human is eating.");
  }
  public void sleep() {
    System.out.println("Human is sleeping.");
  }
}

class Robot implements Workable {
  pubic void work() {
    System.out.println("Robot is working.");
  }
  public void charge() {
    System.out.println("Robot is charging.");
  }
}

**Key Differences Between SRP and ISP**
Priciple: SRP (Single Responsibility Principle)
Focus: class
Problems it solves: A class doing too many things.
Solution: Split responsibilities into separate classes.


Principle: ISP (Interface Segregatioin Principle)
Focus: Interface
Problem it solves: A class being forced to implement unnecessary methods.
Solution: Create smaller, focused interfaces.



In E-commerce App
Example 1: Order Processing
A single interface forces all clients to implemetn unused methods.
Large Interface (Violates ISP)

public interface OrderService {
  void placeOrder();
  void processPayment();
  void generateInvoice();
  void shipOrder();
}

* Implementing for Online Orders (OK)
public class OnlineOrderService implements OrderService {
  @Override
  public void placeOrder() {
    System.out.println("Placing online order.");
  }
  @Override
  public void processPayment() {
    System.out.println("Processing online payment.");
  }
  @Override
  public void generateInvoice() {
    System.out.println("Generating invoice.");
  }
  @Override
  public void shipOrder() {
    System.out.println("Shipping online order.");
  }
}

* Implementing for In-Store Orders (Violates ISP)
public class InStoreOrderService implements OrderService {
  @Override
  public void placeOrder {
    System.out.println("Placing in-store order.");
  }
  @Override
  public void processPayment() {
    System.out.println("Processing in-story payment.");
  }
  @Override
  public void generateInvoice() {
    System.out.println("Generating in-story invoice.");
  }
  @Override
  public void shipOrder() {
    throw new UnsupportedOperationException("In-store orders do not require shipping!");
  }
}

✅ Good Practice (Applying ISP)
We split the interface into smaller, specific ones.

🔹 Step 1: Define Separate Interfaces

public interface OrderProcessing {
    void placeOrder();
}

public interface PaymentProcessing {
    void processPayment();
}

public interface InvoiceGeneration {
    void generateInvoice();
}

public interface ShippingService {
    void shipOrder();
}
🔹 Step 2: Implement Specific Interfaces

public class OnlineOrderService implements OrderProcessing, PaymentProcessing, InvoiceGeneration, ShippingService {
    @Override
    public void placeOrder() {
        System.out.println("Placing online order.");
    }

    @Override
    public void processPayment() {
        System.out.println("Processing online payment.");
    }

    @Override
    public void generateInvoice() {
        System.out.println("Generating invoice.");
    }

    @Override
    public void shipOrder() {
        System.out.println("Shipping online order.");
    }
}

public class InStoreOrderService implements OrderProcessing, PaymentProcessing, InvoiceGeneration {
    @Override
    public void placeOrder() {
        System.out.println("Placing in-store order.");
    }

    @Override
    public void processPayment() {
        System.out.println("Processing in-store payment.");
    }

    @Override
    public void generateInvoice() {
        System.out.println("Generating in-store invoice.");
    }
}
✅ Now, InStoreOrderService does not need to implement shipOrder(), avoiding unnecessary methods.

📌 Example 2: User Authentication in an E-commerce App
A UserService interface forces all users to implement email-based authentication, even if they use social logins.

🚫 Bad Practice (Violating ISP)

public interface UserService {
    void login();
    void resetPassword();
}

public class EmailUserService implements UserService {
    @Override
    public void login() {
        System.out.println("Logging in with email.");
    }

    @Override
    public void resetPassword() {
        System.out.println("Resetting password.");
    }
}

public class SocialUserService implements UserService {
    @Override
    public void login() {
        System.out.println("Logging in with Google.");
    }

    @Override
    public void resetPassword() {
        throw new UnsupportedOperationException("Google login does not support password reset!");
    }
}
🔴 Problem: SocialUserService should not be forced to implement resetPassword().

✅ Good Practice (Applying ISP)
We split the interface into authentication and password management.

🔹 Step 1: Define Smaller Interfaces

public interface LoginService {
    void login();
}

public interface PasswordService {
    void resetPassword();
}
🔹 Step 2: Implement Specific Interfaces

public class EmailUserService implements LoginService, PasswordService {
    @Override
    public void login() {
        System.out.println("Logging in with email.");
    }

    @Override
    public void resetPassword() {
        System.out.println("Resetting password.");
    }
}

public class SocialUserService implements LoginService {
    @Override
    public void login() {
        System.out.println("Logging in with Google.");
    }
}
✅ Now, SocialUserService does not need resetPassword(), following ISP.

📌 Example 3: Product Review System in an E-commerce App
A single interface for reviews forces admins to implement writing reviews, which they don’t need.

🚫 Bad Practice (Violating ISP)

public interface ReviewService {
    void writeReview();
    void approveReview();
}

public class CustomerReviewService implements ReviewService {
    @Override
    public void writeReview() {
        System.out.println("Customer writes a review.");
    }

    @Override
    public void approveReview() {
        throw new UnsupportedOperationException("Customers cannot approve reviews!");
    }
}
🔴 Problem: Customers should not approve reviews, violating ISP.

✅ Good Practice (Applying ISP)
We separate the interface into review writing and review approval.

🔹 Step 1: Define Separate Interfaces

public interface ReviewWriting {
    void writeReview();
}

public interface ReviewApproval {
    void approveReview();
}
🔹 Step 2: Implement Specific Interfaces

public class CustomerReviewService implements ReviewWriting {
    @Override
    public void writeReview() {
        System.out.println("Customer writes a review.");
    }
}

public class AdminReviewService implements ReviewApproval {
    @Override
    public void approveReview() {
        System.out.println("Admin approves a review.");
    }
}
✅ Now, customers only write reviews and admins only approve reviews.

🚀 Summary
Bad Practice ❌	**Good Practice (ISP Applied) ✅
Large interface with many methods	Smaller, focused interfaces
Classes forced to implement unused methods	Only implement necessary methods
Requires handling unsupported operations	No redundant methods
Would you like unit test examples or a real Spring Boot implementation? 🚀
