import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;
import java.io.FileWriter;
import java.io.IOException;

class Main {
    private static List<String> passwordHistory = new ArrayList<>();

    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);

        // Ask the user for password generation parameters
        System.out.println("How many random passwords do you want to generate?");
        int total = getPositiveInteger(in);
        System.out.println("How long do you want each password to be?");
        int length = getPositiveInteger(in);
        System.out.println("Include uppercase letters? (true/false)");
        boolean includeUppercase = in.nextBoolean();
        System.out.println("Include lowercase letters? (true/false)");
        boolean includeLowercase = in.nextBoolean();
        System.out.println("Include numbers? (true/false)");
        boolean includeNumbers = in.nextBoolean();
        System.out.println("Include special characters? (true/false)");
        boolean includeSpecialChars = in.nextBoolean();

        // Create an array to store the random passwords
        String[] randomPasswords = new String[total];

        // Loop over the total number of passwords
        for (int i = 0; i < total; i++) {
            // Generate one random password
            PasswordGenerator passwordGenerator = new PasswordGenerator(length, includeUppercase, includeLowercase, includeNumbers, includeSpecialChars);
            String password = passwordGenerator.generatePassword();

            // Add the random password to the array
            randomPasswords[i] = password;

            // Add the generated password to the password history
            addToPasswordHistory(password);
        }

        // Print out arrays of random passwords
        printPasswords(randomPasswords);

        // Print out the strength of each password and visualize it
        for (String password : randomPasswords) {
            System.out.println("Password: " + password + ", Strength: " + PasswordGenerator.getPasswordStrength(password));
            visualizePasswordStrength(password);
        }

        // Export password history to a file
        exportPasswordHistory();
    }

    public static int getPositiveInteger(Scanner in) {
        int number;
        do {
            number = in.nextInt();
            if (number <= 0) {
                System.out.println("Please enter a positive integer.");
            }
        } while (number <= 0);
        return number;
    }

    public static void printPasswords(String[] arr) {
        for (String password : arr) {
            System.out.println(password);
        }
    }

    public static void visualizePasswordStrength(String password) {
        int strength = PasswordGenerator.getPasswordStrengthLevel(password);
        System.out.print("Strength Visualization: ");
        for (int i = 0; i < strength; i++) {
            System.out.print("*");
        }
        System.out.println();
    }

    public static void addToPasswordHistory(String password) {
        passwordHistory.add(password);
    }

    public static void exportPasswordHistory() {
        try (FileWriter writer = new FileWriter("password_history.txt")) {
            for (String password : passwordHistory) {
                writer.write(password + System.lineSeparator());
            }
            System.out.println("Password history exported successfully to password_history.txt");
        } catch (IOException e) {
            System.err.println("Error exporting password history: " + e.getMessage());
        }
    }
}

class PasswordGenerator {
    private int length;
    private boolean includeUppercase;
    private boolean includeLowercase;
    private boolean includeNumbers;
    private boolean includeSpecialChars;
    private String specialChars = "!@#$%^&*()-_=+[{]};:'\",<.>/?";

    public PasswordGenerator(int length, boolean includeUppercase, boolean includeLowercase, boolean includeNumbers, boolean includeSpecialChars) {
        this.length = length;
        this.includeUppercase = includeUppercase;
        this.includeLowercase = includeLowercase;
        this.includeNumbers = includeNumbers;
        this.includeSpecialChars = includeSpecialChars;
    }

    public String generatePassword() {
        StringBuilder randomPassword = new StringBuilder();
        int totalChars = 0;
        if (includeUppercase) totalChars += 26;
        if (includeLowercase) totalChars += 26;
        if (includeNumbers) totalChars += 10;
        if (includeSpecialChars) totalChars += specialChars.length();

        for (int j = 0; j < length; j++) {
            char randomChar = randomCharacter(totalChars);
            randomPassword.append(randomChar);
        }
        return randomPassword.toString();
    }

    private char randomCharacter(int totalChars) {
        int rand = (int) (Math.random() * totalChars);
        if (includeUppercase && rand < 26) {
            return (char) (rand + 'A');
        }
        rand -= 26;
        if (includeLowercase && rand < 26) {
            return (char) (rand + 'a');
        }
        rand -= 26;
        if (includeNumbers && rand < 10) {
            return (char) (rand + '0');
        }
        rand -= 10;
        if (includeSpecialChars && rand < specialChars.length()) {
            return specialChars.charAt(rand);
        }
        return randomCharacter(totalChars); // Recursively call to ensure selection if no valid character is selected
    }

    public static String getPasswordStrength(String password) {
        int length = password.length();
        if (length < 5) {
            return "Weak";
        } else if (length < 10) {
            return "Medium";
        } else {
            return "Strong and Uncrackable";
        }
    }

    public static int getPasswordStrengthLevel(String password) {
        int length = password.length();
        if (length < 5) {
            return 1; // Weak
        } else if (length < 10) {
            return 2; // Medium
        } else {
            return 3; // Strong and Uncrackable
        }
    }
}
