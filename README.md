# Online-Banking-System
#include <stdio.h>
#include <string.h>

#define MAX_USERS 3
#define PASSWORD_LENGTH 20
#define MAX_TRANSACTIONS 100

// Structure to store user details
typedef struct {
    char username[20];
    char password[PASSWORD_LENGTH];
    float balance;
    int loanEligible;

    // Track deposits
    int depositCount;
    float deposits[MAX_TRANSACTIONS];

    // Track withdrawals
    int withdrawCount;
    float withdrawals[MAX_TRANSACTIONS];
} details;

// Function declarations
int login(details users[], int maxUsers);
void showMenu(details* user);
void deposit(details* user);
void withdraw(details* user);
void checkBalance(details* user);
void showPassbook(details* user);

int main() {
    // Sample users
    details users[MAX_USERS] = {
        {"alice", "alice123", 1200.0, 0, 0, {0}, 0, {0}},
        {"bob", "bobpass", 1500.0, 0, 0, {0}, 0, {0}},
        {"charlie", "charlie321", 1000.0, 0, 0, {0}, 0, {0}}
    };

    printf("=== Welcome to Online Banking System ===\n");

    int userIndex = login(users, MAX_USERS);
    if (userIndex != -1) {
        showMenu(&users[userIndex]);
    } else {
        printf("Login failed. Exiting the system.\n");
    }

    return 0;
}

// Login function
int login(details users[], int maxUsers) {
    char inputUsername[20];
    char inputPassword[PASSWORD_LENGTH];
    int attempts = 3;

    while (attempts > 0) {
        printf("\nLogin:\nUsername: ");
        scanf("%s", inputUsername);
        printf("Password: ");
        scanf("%s", inputPassword);

        for (int i = 0; i < maxUsers; i++) {
            if (strcmp(users[i].username, inputUsername) == 0 &&
                strcmp(users[i].password, inputPassword) == 0) {
                printf("Login successful. Welcome, %s!\n", users[i].username);
                return i;
            }
        }

        attempts--;
        printf("Invalid credentials. Attempts remaining: %d\n", attempts);
    }

    return -1;
}

// Menu function
void showMenu(details* user) {
    int choice;
    do {
        printf("\n--- Menu ---\n");
        printf("1. Check Balance\n");
        printf("2. Deposit\n");
        printf("3. Withdraw\n");
        printf("4. Passbook\n");
        printf("5. Exit\n");
        printf("Choose an option: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                checkBalance(user);
                break;
            case 2:
                deposit(user);
                break;
            case 3:
                withdraw(user);
                break;
            case 4:
                showPassbook(user);
                break;
            case 5:
                printf("Thank you for using Online Banking. Goodbye!\n");
                break;
            default:
                printf("Invalid option. Please try again.\n");
        }
    } while (choice != 5);
}

// Check balance
void checkBalance(details* user) {
    printf("Current Balance: ₹%.2f\n", user->balance);

    if (user->balance >= 20000 && user->loanEligible == 0) {
        user->loanEligible = 1;
        printf("🎉 Congratulations! You are now eligible for a loan of ₹2,00,000.\n");
    }
}

// Deposit function
void deposit(details* user) {
    float amount;
    if (user->depositCount >= MAX_TRANSACTIONS) {
        printf("Deposit limit reached. Cannot record more transactions.\n");
        return;
    }

    printf("Enter amount to deposit: ₹");
    scanf("%f", &amount);

    if (amount > 0) {
        user->balance += amount;
        user->deposits[user->depositCount] = amount;  // Record deposit amount
        user->depositCount++;                           // Increment deposit count
        printf("Deposited successfully. New balance: ₹%.2f\n", user->balance);

        if (amount >= 20000 && user->loanEligible == 0) {
            user->loanEligible = 1;
            printf("🎉 Congratulations! You are now eligible for a loan of ₹2,00,000.\n");
        }
    } else {
        printf("Invalid amount.\n");
    }
}

// Withdraw function
void withdraw(details* user) {
    float amount;
    if (user->withdrawCount >= MAX_TRANSACTIONS) {
        printf("Withdrawal limit reached. Cannot record more transactions.\n");
        return;
    }

    printf("Enter amount to withdraw: ₹");
    scanf("%f", &amount);

    if (amount > 0 && amount <= user->balance) {
        user->balance -= amount;
        user->withdrawals[user->withdrawCount] = amount; // Record withdrawal amount
        user->withdrawCount++;                            // Increment withdrawal count
        printf("Withdrawal successful. New balance: ₹%.2f\n", user->balance);

        if (user->balance < 20000 && user->loanEligible == 1) {
            user->loanEligible = 0;
            printf("⚠️ Your balance is below ₹20,000. Loan eligibility revoked.\n");
        }
    } else {
        printf("Insufficient balance or invalid amount.\n");
    }
}

// Show passbook function
void showPassbook(details* user) {
    printf("\n--- Passbook ---\n");

    printf("Deposits (%d):\n", user->depositCount);
    if (user->depositCount == 0) {
        printf("  No deposits made yet.\n");
    } else {
        for (int i = 0; i < user->depositCount; i++) {
            printf("  ₹%.2f\n", user->deposits[i]);
        }
    }

    printf("Withdrawals (%d):\n", user->withdrawCount);
    if (user->withdrawCount == 0) {
        printf("  No withdrawals made yet.\n");
    } else {
        for (int i = 0; i < user->withdrawCount; i++) {
            printf("  ₹%.2f\n", user->withdrawals[i]);
        }
    }
}
