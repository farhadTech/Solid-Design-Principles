# Single Responsibility Principle (SRP)
>Definition: A class should have only one reason to change.
This means a class should do only one thing and be responsible for one piece of functionality.

### How does this principle help us to build better software?
** 1. Testing - A class with one responsibility will have far fewer test cases.
** 2. Lower coupling - Less functionality in a single class will have fewer dependencies.
** 3. Organization - Smaller, well-organized classes are easier to search than monolithic ones.

**Example 1: User Management**

-Bad Practice -> Violating Single Responsibility Principle

**1. Database operations
2. Password hashing
3. Email notification**

![page1](images/page1.png)

```
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    public void registerUser(User user) {
        String hashedPassword = hashPassword(user.getPassword());
        user.setPassword(hashedPassword);
        userRepository.save(user);
        sendWelcomeEmail(user.getEmail());
    }

    private String hashPassword(String password) {
        return BCrypt.hashpw(password, BCrypt.gensalt());
    }

    private void sendWelcomeEmail(String email) {
        System.out.println("Sending welcome email to: " + email);
    }
}
```

**Good Practice -> Single Responsibility Principle Applied**

![page2](images/page2.png)

```
@Service
public class PasswordService {
    public String hashPassword(String password) {
        return BCrypt.hashpw(password, BCrypt.gensalt());
    }
}
```

```
@Service
public class EmailService {
    public void sendWelcomeEmail(String email) {
        System.out.println("Sending welcome email to: " + email);
    }
}
```

```
@Service
public class UserService {
    private final UserRepository userRepository;
    private final PasswordService passwordService;
    private final EmailService emailService;

    public UserService(UserRepository userRepository, PasswordService passwordService, EmailService emailService) {
        this.userRepository = userRepository;
        this.passwordService = passwordService;
        this.emailService = emailService;
    }

    public void registerUser(User user) {
        user.setPassword(passwordService.hashPassword(user.getPassword()));
        userRepository.save(user);
        emailService.sendWelcomeEmail(user.getEmail());
    }
}

```
Single Responsibility Principles Benefits:
-> Code is now modular and reusable. <br>
-> Easier to test password hashing, email, and user management separately. <br>
-> Future changes: changing hashing method don't affect the UserService. <br>


Example 2: Product Management in an E-commerce App

Bad Practice (All Responsibilities in one class)

```
@Service
public class ProductService {
    @Autowired
    private productRepository productRepository;

    public void addProduct(Product product) {
        validateProduct(product);
        productRepository.save(product);
        logProductAddition(product);
    }

    private void vaidateProduct(Product product) {
        if(product.getPrice() <= 0) {
            throw new IllegalArgumentException("Invalid product price.");
        }
    }

    private void logProductAddition(Product product) {
        System.out.println("Product added: " + product.getName());
    }
}
```

Good Practice (Single Responsibility Principle Applied)
We split responsibilites into three classes:

1. ProductService -> Handles prouct operations
2. ProductValidator -> Validates product data
3. LoggerService -> Logs product additions

```
@Component
public class ProductValidator {
    public void validate(Product product) {
        if(product.getPrice() <= 0) {
            throw new IllegalArgumentException("Invalid product price.");
        }
    }
}
```

```
@Service
public class LoggerService {
    public void log(String message) {
        System.out.println("LOG: " + message);
    }
}
```

```
@Service
public class ProductService {
    private final productRepository productRepository;
    private final productValidator productValidator;
    private final LoggerService loggerService;

    public ProductService(ProductRepository productRepository, ProductValidator productValidator, LoggerService loggerService) {
        this.productRepository = productRepository;
        this.loggerService = loggerService;
        this.productValidator = productValidator;
    }

    public void addProduct(Product product) {
        productValidator.validate(product);
        productRepository.save(product);
        loggerService.log("Product added: " + product.getName());
    }
}
```
**Benefits:
1. Easy to extend - can modify validation without touching ProductService.
2. Better reusability - LoggerService can be used in other classes