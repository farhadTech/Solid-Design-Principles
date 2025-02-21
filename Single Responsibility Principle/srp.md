
Example 1: User Management

Bad Prctice -> Violating Single Responsibility Principle
1. Database operations
2. Password hashing
3. Email notification

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

Good Practice -> Single Responsibility Principle Applied

@Service
public class PasswordService {
    public String hashPassword(String password) {
        return BCrypt.hashpw(password, BCrypt.gensalt());
    }
}

@Service
public class EmailService {
    public void sendWelcomeEmail(String email) {
        System.out.println("Sending welcome email to: " + email);
    }
}

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

Single Responsibility Principles Benefits:
-> Code is now modular and reusable
-> Easier to test password hashing, email, and user management separately.
-> Future changes: changing hashing method don't affect the UserService.
