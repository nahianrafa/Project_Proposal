import java.util.*;

class User {
    private String userId;
    private String pin;
    private double balance;
    private List<String> transactionHistory = new ArrayList<>();

    public User(String userId, String pin, double balance) {
        this.userId = userId;
        this.pin = pin;
        this.balance = balance;
    }

    public boolean authenticate(String inputPin) {
        return this.pin.equals(inputPin);
    }

    public double getBalance() {
        return balance;
    }

    public void withdraw(double amount) {
        if (amount <= balance) {
            balance -= amount;
            logTransaction("Withdrawn: $" + amount);
        } else {
            System.out.println("Insufficient balance.");
        }
    }

    public void deposit(double amount) {
        balance += amount;
        logTransaction("Deposited: $" + amount);
    }

    public void transfer(User receiver, double amount) {
        if (amount <= balance) {
            balance -= amount;
            receiver.deposit(amount);
            logTransaction("Transferred: $" + amount + " to " + receiver.getUserId());
        } else {
            System.out.println("Insufficient balance.");
        }
    }

    public String getUserId() {
        return userId;
    }

    private void logTransaction(String transaction) {
        transactionHistory.add(transaction + " | " + new Date());
        sendRealTimeAlert(transaction);
    }

    public void showTransactionHistory() {
        System.out.println("Transaction History for " + userId + ":");
        for (String t : transactionHistory) {
            System.out.println(t);
        }
    }

    private void sendRealTimeAlert(String message) {
        System.out.println("[ALERT] " + message);
    }
}

public class ATMSimulator {
    private static Map<String, User> users = new HashMap<>();

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        // Sample users
        users.put("user1", new User("user1", "1234", 1000));
        users.put("user2", new User("user2", "5678", 2000));

        System.out.println("Enter User ID:");
        String userId = sc.nextLine();

        if (!users.containsKey(userId)) {
            System.out.println("User not found.");
            return;
        }

        User user = users.get(userId);

        System.out.println("Enter PIN:");
        String pin = sc.nextLine();

        if (!user.authenticate(pin)) {
            System.out.println("Invalid PIN.");
            return;
        }

        while (true) {
            System.out.println("\n1. Balance Inquiry\n2. Withdraw Cash\n3. Transfer Funds\n4. Transaction History\n5. Exit");
            int choice = sc.nextInt();

            switch (choice) {
                case 1:
                    System.out.println("Your balance is: $" + user.getBalance());
                    break;
                case 2:
                    System.out.println("Enter amount to withdraw:");
                    double amount = sc.nextDouble();
                    user.withdraw(amount);
                    break;
                case 3:
                    sc.nextLine(); // consume newline
                    System.out.println("Enter recipient User ID:");
                    String recipientId = sc.nextLine();
                    if (users.containsKey(recipientId)) {
                        System.out.println("Enter amount to transfer:");
                        double transferAmount = sc.nextDouble();
                        user.transfer(users.get(recipientId), transferAmount);
                    } else {
                        System.out.println("Recipient not found.");
                    }
                    break;
                case 4:
                    user.showTransactionHistory();
                    break;
                case 5:
                    System.out.println("Thank you for using the ATM. Goodbye!");
                    return;
                default:
                    System.out.println("Invalid choice. Try again.");
            }
        } 
    }
}
