import java.util.*;

class User {
    private int userId;
    private int pin;
    private Account account;

    public User(int userId, int pin, double balance) {
        this.userId = userId;
        this.pin = pin;
        this.account = new Account(balance);
    }

    public boolean authenticate(int inputPin) {
        return this.pin == inputPin;
    }

    public Account getAccount() {
        return account;
    }

    public int getUserId() {
        return userId;
    }
}

class Account {
    private double balance;
    private List<String> transactions;

    public Account(double balance) {
        this.balance = balance;
        transactions = new ArrayList<>();
    }

    public double getBalance() {
        return balance;
    }

    public boolean withdraw(double amount) {
        if (amount <= balance) {
            balance -= amount;
            transactions.add("Withdrew ₹" + amount);
            return true;
        }
        return false;
    }

    public void deposit(double amount) {
        balance += amount;
        transactions.add("Deposited ₹" + amount);
    }

    public void transfer(Account receiver, double amount) {
        if (withdraw(amount)) {
            receiver.deposit(amount);
            transactions.add("Transferred ₹" + amount + " to another account");
        }
    }

    public void showTransactions() {
        System.out.println("Transaction History:");
        for (String t : transactions) {
            System.out.println("- " + t);
        }
    }
}

public class ATMSystem {
    private static Map<Integer, User> users = new HashMap<>();
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        users.put(1001, new User(1001, 1234, 5000.00));
        users.put(1002, new User(1002, 5678, 3000.00));

        System.out.println("Welcome to ATM System");
        System.out.print("Enter User ID: ");
        int userId = scanner.nextInt();
        System.out.print("Enter PIN: ");
        int pin = scanner.nextInt();

        User user = users.get(userId);

        if (user != null && user.authenticate(pin)) {
            System.out.println("Login successful!");
            runATM(user);
        } else {
            System.out.println("Invalid credentials.");
        }
    }

    public static void runATM(User user) {
        int choice;
        do {
            System.out.println("\n1. Balance Inquiry\n2. Withdraw\n3. Transfer\n4. View Transactions\n5. Exit");
            System.out.print("Enter choice: ");
            choice = scanner.nextInt();
            Account account = user.getAccount();

            switch (choice) {
                case 1:
                    System.out.println("Your current balance is: ₹" + account.getBalance());
                    break;
                case 2:
                    System.out.print("Enter amount to withdraw: ₹");
                    double amount = scanner.nextDouble();
                    if (account.withdraw(amount)) {
                        System.out.println("Withdrawal successful.");
                    } else {
                        System.out.println("Insufficient balance.");
                    }
                    break;
                case 3:
                    System.out.print("Enter receiver User ID: ");
                    int receiverId = scanner.nextInt();
                    System.out.print("Enter amount to transfer: ₹");
                    double transferAmt = scanner.nextDouble();
                    User receiver = users.get(receiverId);
                    if (receiver != null) {
                        account.transfer(receiver.getAccount(), transferAmt);
                        System.out.println("Transfer successful.");
                    } else {
                        System.out.println("Receiver not found.");
                    }
                    break;
                case 4:
                    account.showTransactions();
                    break;
                case 5:
                    System.out.println("Thank you for using the ATM System.");
                    break;
                default:
                    System.out.println("Invalid option.");
            }
        } while (choice != 5);
    }
}
