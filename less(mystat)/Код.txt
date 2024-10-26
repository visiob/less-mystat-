#include <iostream>
#include <fstream>
#include <Windows.h>
#include <vector>
#include <string>
#include <ctime>
using namespace std;

class MyStat {
    vector<string> homework;
public:
    void load() {
        ifstream file("hw.txt");

        string line;
        while (getline(file, line)) {
            homework.push_back(line);
        }

        file.close();
    }
    void showAllHomework() {
        if (homework.empty()) {
            cout << "Домашніх завдань немає" << endl;
        }
        else {
            cout << "Домашні завдання:\n";
            for (string assignment : homework) {
                cout << assignment << endl << endl;
            }
        }
    }
    void findHomework(int index) {
        if (index < 1 || index > homework.size()) {
            cout << "Завдання не знайдено" << endl;
        }
        else {
            cout << "Домашнє завдання #" << index << ": " << homework[index - 1] << endl;
            cout << "Помітити завдання як виконане(так/ні): ";
            string ch;
            cin >> ch;
            if (ch == "так") {
                homework.erase(homework.begin() + index - 1);
            }
        }
    }
};

class Account {
	string username;
	string password;
	MyStat account;
    int crystals;
    int coins;
    int rate;
public:
    //конструктор конкретного аккаунту (також генерує всю валюту на аккаунті та завантажує домашні завдання для окремого аккаунту)
	Account(string u, string p) : username(u), password(p) {
        crystals = rand() % 100;
        coins = rand() % 300;
        rate = crystals + coins;
        account.load();
    }
    //перевірка чи введені логін та пароль відповідають аккаунту 
    bool login(string u, string p) {
        return (username == u && password == p);
    }
    //виводить інформацію про акканту
    void profile() {
        cout << "Ім'я: " << username << "\nCrystals: " << crystals << "\nCoins: " << coins << "\nRating: " << rate << endl;
    }
    //повертає посилання на об’єкт MyStat цього акаунту для роботи з домашніми завданнями
    MyStat& getMyStat() {
        return account;
    }
};

class Login {
	vector<Account> accounts;
public:
    //конструктор витягує з файлу та зберігає у масив accounts всі аккаунти
    Login(const string& filename) {
        ifstream file(filename);

        string username, password;
        while (file >> username >> password) {
            accounts.push_back({ username, password });
        }

        file.close();
    }

    //метод авторизації в конкретний аккаунт (всі аккаунти можна подивитись у файлі data.txt)
    Account* signIn() {
        string username, password;
        cout << "Введіть логін: ";
        cin >> username;
        cout << "Введіть пароль: ";
        cin >> password;

        for (Account& account : accounts) {
            if (account.login(username, password)) {
                system("cls");
                account.profile();
                return &account;
            }
        }
        return nullptr;
    }
};

int main() {
	SetConsoleCP(1251);
	SetConsoleOutputCP(1251);
    srand(time(NULL));

    Login login("data.txt");

    Account* currentUser = nullptr;//каюся чат гпт допоміг мені з усією системою отримання поточного аккаунту, але мені так сподоалась моя задумка, що я не міг її залишити незакінченою  
    while (currentUser == nullptr) {
        currentUser = login.signIn();
        if (!currentUser) system("cls");
    }

    int choice;
    for (;;) {
        cout << "\nОберіть дію:" << endl;
        cout << "1. Показати всі домашні завдання" << endl; 
        cout << "2. Знайти домашнє завдання" << endl;
        cout << "3. Змінти акаунт" << endl;
        cout << "4. Вийти з програми" << endl;
        cin >> choice;

        switch (choice) {
        case 1:
            currentUser->getMyStat().showAllHomework();
            break;
        case 2: {
            int index;
            cout << "Введіть номер домашнього завдання, яке хочете знайти: ";
            cin >> index;
            currentUser->getMyStat().findHomework(index);
            break;
        }
        case 3:
            system("cls");
            currentUser = nullptr;
            while (currentUser == nullptr) {
                currentUser = login.signIn();
                if (!currentUser) 
                    system("cls");
            }
            break;
        case 4:
            return 0;
            break;
        default:
            cout << "Помилка!" << endl;
        }
    }
}