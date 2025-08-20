# Simple-ATM-Simulation
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
using namespace std;

struct Account {
    string userID;
    int pin;
    double balance;
};

class ATMSystem {
private:
    vector<Account> accounts;
    int currentIndex = -1;

public:
    ATMSystem() {
        loadFromFile();
    }

    void loadFromFile() {
        ifstream file("accounts.txt");
        accounts.clear();
        if (file.is_open()) {
            string id;
            int p;
            double b;
            while (file >> id >> p >> b) {
                accounts.push_back({id, p, b});
            }
            file.close();
        }
        // Default accounts if file is empty
        if (accounts.empty()) {
            accounts.push_back({"user1", 1111, 2000});
            accounts.push_back({"user2", 2222, 3000});
            accounts.push_back({"user3", 3333, 5000});
            saveToFile();
        }
    }

    void saveToFile() {
        ofstream file("accounts.txt");
        if (file.is_open()) {
            for (auto &acc : accounts) {
                file << acc.userID << " " << acc.pin << " " << acc.balance << "\n";
            }
            file.close();
        }
    }

    bool authenticate(string id, int p) {
        for (int i = 0; i < accounts.size(); i++) {
            if (accounts[i].userID == id && accounts[i].pin == p) {
                currentIndex = i;
                return true;
            }
        }
        return false;
    }

    void displayMenu() {
        cout << "\n===== ATM MENU =====\n";
        cout << "1. Balance Inquiry\n";
        cout << "2. Deposit\n";
        cout << "3. Withdraw\n";
        cout << "4. Exit\n";
        cout << "====================\n";
    }

    void balanceInquiry() {
        cout << "\nYour current balance is: ₹" << accounts[currentIndex].balance << endl;
    }

    void deposit(double amount) {
        if (amount > 0) {
            accounts[currentIndex].balance += amount;
            cout << "\nDeposited ₹" << amount 
                 << ". New balance: ₹" << accounts[currentIndex].balance << endl;
        } else {
            cout << "\nInvalid deposit amount!" << endl;
        }
    }

    void withdraw(double amount) {
        if (amount > 0 && amount <= accounts[currentIndex].balance) {
            accounts[currentIndex].balance -= amount;
            cout << "\nWithdrew ₹" << amount 
                 << ". New balance: ₹" << accounts[currentIndex].balance << endl;
        } else {
            cout << "\nInsufficient balance or invalid amount!" << endl;
        }
    }

    void run() {
        string enteredID;
        int enteredPin;
        cout << "Enter User ID: ";
        cin >> enteredID;
        cout << "Enter PIN: ";
        cin >> enteredPin;

        if (!authenticate(enteredID, enteredPin)) {
            cout << "\nAuthentication failed! Exiting...\n";
            return;
        }

        int choice;
        double amount;
        do {
            displayMenu();
            cout << "Enter your choice: ";
            cin >> choice;

            switch (choice) {
                case 1: balanceInquiry(); break;
                case 2: cout << "Enter amount to deposit: "; cin >> amount; deposit(amount); break;
                case 3: cout << "Enter amount to withdraw: "; cin >> amount; withdraw(amount); break;
                case 4: cout << "\nThank you for using the ATM!\n"; saveToFile(); break;
                default: cout << "\nInvalid choice! Try again.\n";
            }
        } while (choice != 4);
    }
};

int main() {
    cout << "===== Welcome to Multi-User ATM Simulation =====\n";
    ATMSystem atm;
    atm.run();
    return 0;
}
