#The Open/Closed Principle with Code Examples

Robert C. Martin considered this principle as "the most important principle of object-oriented design". But he wasn't the first one who defined it. Bertrand Meyer wrote about it in 1988 in his book Object-Oriented Software Construction. He Explained the Open/Closed Principle as:

> "Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification."

The general idea of this principle is great. It tells you to write your code so that you will be able to add new functionality without changig the existing code. That prevents situations in which a change to one of your classes also requires you to adapt all depending classes. Unfortunately, Bertrand Mayer proposes to use inheritance to achieve this goal:

> "A class is closed, since it may be compiled, stored in a library, baselined, and used by client classes. But it is also open, since any new class may use it as parent, adding new features. When a descendant class is defined, there is no need to change the original or to disturb its clients."

But as we've learned over the years and as other authors explained in great details, e.g., Robert C. Martin in his articles about the SOLID principles or Joshua Bloch in his book Effective Java, inheritance introduces tight coupling if the subclasses depend on implementation details of their parent class.

That's why Robert C. Martin and others redefined the Open/Closed Principle to the Polymorphic Open/Closed Principle. It uses interfaces instead of superclasses to allow different implementations which you can easily substitute without changing the code that uses them. The interfaces are closed for modifications, and you can provide new implementations to extend the functionality of your software.

The main benefit of this approach is that an interface introduces an additional level of abstraction which enables loose coupling. The implementations of an interface are independent of each other and don't need to share any code. If you cosider it beneficial that two implementations of an interface share some code, you can either use inheritance or composition.

Bad Practice (Violating Open/Closed Principle)

enum AttactType {
  NORMAL, FIRE, EARTH
}

public class Character {
  public void attact(AttackType attackType) {
    switch(attactType) {
      case EARTH:
        System.out.println("Earth attack");
        break;
      case FIRE:
        System.out.println("Fire attack");
        break;
      case NORMAL:
        System.out.println("Normal attack");
        break;
    // If we want to add new attach type, we must modify this class!
    }
  }
}

Good Practice:

interface IAttackType {
  void perform();
}

class EarthAttack implements IAttackType {
  public void perform() {
    System.out.println("Earth attack");
  }
}

class FireAttack implements IAttackType {
  public void perform() {
    System.out.println("Fire attack");
  }
}

class NormalAttack implements IAttackType {
  public void perform() {
    System.out.println("Normal attack");
  }
}

class Character {
  private final IAttackType attackType;

  public Character(IAttackType attackType) {
    this.attackType = attackType;
  }

  public void attack() {
    attackType.perform();
  }
}

public class Main{
  public static void main(String[] args) {
    Character fireAttack = new Character(new FireAttack());
    fireAttack.attack();

    Character earthAttack = new Character(new EarthAttack());
    earthAttack.attack();

    Character normalAttack = new Character(new NormalAttack());
    normalAttack.attack();

    // Adding a new attack type requires No modification to existing classes!

  }
}

We are using polymorphism to extend the behaviour without modifying the existing code.

Understanding the Open/Closed Principle with a Coffee Machine Example:
To understand this, let's imagine a coffee machine. There are basic machines that make only filter coffee, which premium ones also grind beans and brew expresso.

Insteam of manually changing the code whenever we upgrade out coffee machine, we can desing the system in a way that allows adding new machines without modifying existing code. Let's see how we can achieve this using java.

Step 1: Basic Coffee Machine implementation
We first create a BasicCoffeeMachine class that can brew only filter coffee.

public class BasicCoffeeMachine {
    private Map<CoffeeSelection, GroundCoffee> groundCoffee;
    private BrewingUnit brewingUnit;

    public BasicCoffeeMachine(Map<CoffeeSelection, GroundCoffee> coffee) {
        this.groundCoffee = coffee;
        this.brewingUnit = new BrewingUnit();
    }

    public Coffee brewCoffee(CoffeeSelection selection) {
        GroundCoffee coffee = this.groundCoffee.get(CoffeeSelection.FILTER_COFFEE);
        return this.brewingUnit.brew(CoffeeSelection.FILTER_COFFEE, coffee, 480);
    }
}

* This class can only brew filter coffee
* It directly handles the brewing process.
* If we need a new type of coffee machine, we'll have to modify this class, which violates the Open/Closed Principle.

Step 2: Controlling the Coffee Machine with an App
Now, we create a BasicCoffeeApp that controls the BasicCoffeeMachine

public class BasicCoffeeApp {
    private BasicCoffeeMachine coffeeMachine;

    public BasicCoffeeApp(BasicCoffeeMachine coffeeMachine) {
        this.coffeeMachine = coffeeMachine;
    }

    public Coffee prepareCoffee(CoffeeSelection selection) {
        return this.coffeeMachine.brewCoffee(selection);
    }
}

* This app is directly tied to BasicCoffeeMachine.
* If we replace BasicCoffeeMachine with a better machine, we'll have to rewrite the app, which is not deal.

Step 3: Using the Open/Closed Principle
To make our system flexible we introduce an interface that all coffee machines will implement.

public interface CoffeeMachine {
  Coffee brewCoffee(CoffeeSelection selection);
}

* Now, any coffee machine can implement this interface, making our system open to extension.
* But we don't have to modify existing code, keeping it close to modificaiton.

public class BasicCoffeeMachine implements CoffeeMachine {
  @Override
  public Coffee brewCoffee(CoffeeSelection selection) {
    // Same brewing logic as before
  }
}

Step 4: Adding a Premium Coffee Machine
Now, we can easily add a new type of coffee machine that can brew espresso and filter coffee.

public class PremiumCoffeeMachine implements CoffeeMachine {
    private Grinder grinder;
    private BrewingUnit brewingUnit;

    public PremiumCoffeeMachine(Map<CoffeeSelection, CoffeeBean> beans) {
        this.grinder = new Grinder();
        this.brewingUnit = new BrewingUnit();
    }

    @Override
    public Coffee brewCoffee(CoffeeSelection selection) {
        switch (selection) {
            case ESPRESSO: return brewEspresso();
            case FILTER_COFFEE: return brewFilterCoffee();
            default: throw new CoffeeException("Coffee type not supported!");
        }
    }

    private Coffee brewEspresso() {
        // Grind beans and brew espresso
    }

    private Coffee brewFilterCoffee() {
        // Brew filter coffee
    }
}

Step 5: Updating the Coffee App
Now, we update our CoffeeApp to work with any coffee machine.

public class CoffeeApp {
  private CoffeeMachine coffeeMachine;

  public CoffeeApp(CoffeeMachine coffeeMachine) {
    this.coffeeMachine = coffeeMachine;
  }

  public coffee prepareCoffee(CoffeeSelection selection) {
    return this.coffeeMachine.brewCoffee(selection);
  }
}

Here are some more examples:

Example 1: Payment System
Bad Practice (Vilating Open Closed principle)

@Service
public class paymentService {
  public void processPayment(String paymentType) {
    if(paymentType.equals("CREDIT")) {
      System.out.println("Processing credit card payment...")
    } else if (paymentType.equals("PAYPAL")) {
      System.out.println("Processing Paypal card payment...")
    } else{
        throw new IllegalArgumentException("Unsupported payment method");
    }
  }
}

Problem: Every time a new payment method (e.g., Google Pay) is added, we must modify PaymentService.

This violates Open Closed Principle because we are modifying existing code instead of extending it.

Good Practice:

step 1: Create a payment interface
public interface payment

public interface Payment {
  void processPayment();
}

Step 2: Implement Concrete Payment
@Service
public class CreditCardPayment implement payment {
  @Override
  public void processPayment() {
    System.out.println("Processing credit card payment...");
  }
}

@Service
public class PaypalPayment implement payment {
  @Override
  public void processPayment() {
    System.out.println("Processing Paypal payment...");
  }
}

Step 3: Create a Flexible PaymentService
Instead of modifying this class we inject different payment dynamically.

@Service
public class PaymentService {
  private final Map<String, payment> payments;

  @Autowired
  public PaymentService(List<Payment> payments) {
    this.payment = payment.stream()
      .collect(Collectors.toMap(p -> p.getClass().getSimpleNam(), p -> ));
  }

  public void processPayment(String paymentType) {
    payment p = payments.get(paymentType + "Payment");

    if(p == null) {
      throw new IllegalArgumentException("Unsupported payment metod");
    }
    p.processPayment();
  }
}

@Service
public class GooglePayPayment implements Payment {
  @Override
  public void processPayment() {
    System.out.println("Processing Google pay payment...");
  }
}

Open/Closed Principle Benefits in This case
* New payment methods can be added without modifying existing code.
* Follows Dependency Injection and SOLID principles.
* Easier to test -> Each payment method is independent.

Example 2: Discount System in an E-commerce App
Bad Practice

@Service
public class DiscountService {
  public double applyDiscount(String discountType, double price) {
    if(discountType.equals("SEASONAL")) {
      return price * 0.9;
    } else if(discountType.equals("BLACK_FRIDAY")) {
      return price * 0.7;
    } else {
      return price;
    }
  }
}

Good Practice

Step 1: Create a DiscountStrategy Interface.
public interface DiscountStrategy {
  double applyDiscount(double price);
}

Step 2: Implement Different Discount Strategies
@Service
public class SeasonalDiscount implements DiscountStrategy {
  @Override
  public double applyDiscount(double price) {
    return price * 0.9;
  }
}

Step 3: Create a Flexible DiscountService
@Service
public class DiscountService {
  private final Map<String, DiscountStrategy> discountStrategies;

  @Autowired
  public DiscountService(List<DiscountStrategy> discountStrategies) {
    this.discountStrategies = discountStratgies.stream()
    .collect(Collectors.toMap(s -> s.getClass().getSimpleName(), s -> s));
  }

  pubic double applyDiscount(String discountType, double price) {
    DiscountStrategy strategy = discountStrategies.get(discountType + "Discount");
    if(strategy == null) {
      return price;
    }
    return strategy.applyDiscount(price);
  }
}

Now, to add a "EID_DISCOUNT", we create a new class without modifying existing code.

@Service
public class NewYearDiscount implements DiscountStrategy {
  @Override
  public double applyDiscount(double price) {
    return price * 0.85;
  }
}

Open Closed Principle Benefits in This case
* Easy to extend without modifying DiscountService
* No risk of breaking existing discount logic.
* Better separation of concerns and modular code.

Example 3: Order Processing with Different Tax Calculations
Bad Practice
@Service
public class TaxCalculator {
  public double calculateTax(String country, double amount) {
    if(country.equals("USA")) {
      return amount * 0.1;
    } else if(country.equals("UK")) {
      return amount * 0.2;
    } else {
      return 0;
    }
  }
}

Good Practice
Step 1: Create a TaxStrategy Interface
public interface TaxStrategy {
  double calculateTax(double amount);
}

Step 2: Implement Different Tax Strategies
@Service
public class USATax implements TaxStrategy {
  @Override
  public double calculateTax(double amount) {
    return amount * 0.1;
  }
}

@Service
public class UKTax implements TaxStrategy {
  @Override
  public double calculateTax(double amount) {
    return amount * 0.2;
  }
}

Step 3: Create a Flexible TaxService
@Service
public class TaxService {
  private final Map<String, TaxStrategy> taxStrategies;

  @Autowired
  public TaxService(List<TaxStrategy> taxStrategies) {
    this.taxStrategies = taxStrategies.stream()
      .collect(Collectors.toMap(s -> s.getClass().getSimpleName(), s -> s));
  }

  public double calculateTax(String country, double amount) {
    TaxStrategy strategy = taxStrategies.get(country + "Tax");

    if(strategy == null) {
      return 0;
    }
    return strategy.calculateTax(amount);
  }
}

Now, to add "india Tax", we create a new class without modifying TaxService;
@Service
public class IndiaTax implements TaxStrategy {
  @Override
  public double calculateTax(double amount) {
    return amount * 0.18;
  }
}

Summary:
Bad Practice:
One class handles multiple cases
Hard to extend
High risk of breaking existing code

Good Practice
Uses separate classes per case
Easily extendable
No modification needed for new cases.

