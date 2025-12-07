#include <iostream>
#include <fstream>
#include <string>
#include <cstdio>
using namespace std;

#define STUD_FILE "students.txt"
#define CRE_FILE  "credentials.txt"

string currentUser;
string currentRole;

// ---------------------- LOGIN ----------------------

bool login() {
    string u, p, r;
    string inUser, inPass;

    cout << "USERNAME: ";
    cin >> inUser;
    cout << "PASSWORD: ";
    cin >> inPass;

    ifstream file(CRE_FILE);
    if (!file) {
        cout << "Credential file missing!\n";
        return false;
    }

    while (file >> u >> p >> r) {
        if (inUser == u && inPass == p) {
            currentUser = u;
            currentRole = r;
            return true;
        }
    }
    return false;
}

// ---------------------- ADD STUDENT ----------------------

void addStudent() {
    int roll;
    string name;
    float mark;

    cout << "Roll: ";
    cin >> roll;
    cout << "Name: ";
    cin.ignore();
    getline(cin, name);
    cout << "Mark: ";
    cin >> mark;

    ofstream file(STUD_FILE, ios::app);
    file << roll << "|" << name << "|" << mark << "\n";

    cout << "Student added!\n";
}

// ---------------------- DISPLAY STUDENTS ----------------------

void displayStudents() {
    ifstream file(STUD_FILE);
    if (!file) {
        cout << "No student file!\n";
        return;
    }

    int roll;
    string name;
    float mark;
    char sep;

    cout << "\nRoll\tName\tMark\n";
    cout << "-----------------------------\n";

    while (file >> roll >> sep) {
        getline(file, name, '|');
        file >> mark;
        cout << roll << "\t" << name << "\t" << mark << "\n";
    }
}

// ---------------------- SEARCH STUDENT ----------------------

void searchStudent() {
    int findRoll;
    cout << "Enter roll to search: ";
    cin >> findRoll;

    ifstream file(STUD_FILE);

    int roll;
    string name;
    float mark;
    char sep;

    while (file >> roll >> sep) {
        getline(file, name, '|');
        file >> mark;

        if (roll == findRoll) {
            cout << "Found: " << roll << " " << name << " " << mark << "\n";
            return;
        }
    }

    cout << "Student not found!\n";
}

// ---------------------- DELETE STUDENT ----------------------

void deleteStudent() {
    int delRoll;
    cout << "Enter roll to delete: ";
    cin >> delRoll;

    ifstream file(STUD_FILE);
    ofstream temp("temp.txt");

    int roll;
    string name;
    float mark;
    char sep;
    bool found = false;

    while (file >> roll >> sep) {
        getline(file, name, '|');
        file >> mark;

        if (roll != delRoll)
            temp << roll << "|" << name << "|" << mark << "\n";
        else
            found = true;
    }

    file.close();
    temp.close();

    remove(STUD_FILE);
    rename("temp.txt", STUD_FILE);

    if (found) cout << "Student deleted!\n";
    else cout << "Roll not found!\n";
}

// ---------------------- UPDATE STUDENT ----------------------

void updateStudent() {
    int updateRoll;
    cout << "Enter roll to update: ";
    cin >> updateRoll;

    ifstream file(STUD_FILE);
    ofstream temp("temp.txt");

    int roll;
    string name, newName;
    float mark, newMark;
    char sep;
    bool found = false;

    while (file >> roll >> sep) {
        getline(file, name, '|');
        file >> mark;

        if (roll == updateRoll) {
            found = true;
            cout << "New Name: ";
            cin.ignore();
            getline(cin, newName);
            cout << "New Mark: ";
            cin >> newMark;

            temp << roll << "|" << newName << "|" << newMark << "\n";
        } else {
            temp << roll << "|" << name << "|" << mark << "\n";
        }
    }

    file.close();
    temp.close();

    remove(STUD_FILE);
    rename("temp.txt", STUD_FILE);

    if (found) cout << "Student updated!\n";
    else cout << "Roll not found!\n";
}

// ---------------------- MENUS ----------------------

void adminMenu() {
    int c;
    while (true) {
        cout << "\nADMIN MENU\n";
        cout << "1.Add\n2.Display\n3.Search\n4.Update\n5.Delete\n6.Logout\n";
        cin >> c;

        if (c == 1) addStudent();
        else if (c == 2) displayStudents();
        else if (c == 3) searchStudent();
        else if (c == 4) updateStudent();
        else if (c == 5) deleteStudent();
        else return;
    }
}

void staffMenu() {
    int c;
    while (true) {
        cout << "\nSTAFF MENU\n";
        cout << "1.Add\n2.Display\n3.Search\n4.Update\n5.Logout\n";
        cin >> c;

        if (c == 1) addStudent();
        else if (c == 2) displayStudents();
        else if (c == 3) searchStudent();
        else if (c == 4) updateStudent();
        else return;
    }
}

void guestMenu() {
    int c;
    while (true) {
        cout << "\nGUEST MENU\n";
        cout << "1.Display\n2.Search\n3.Logout\n";
        cin >> c;

        if (c == 1) displayStudents();
        else if (c == 2) searchStudent();
        else return;
    }
}

// ---------------------- MAIN ----------------------

int main() {
    if (!login()) {
        cout << "Invalid login!\n";
        return 0;
    }

    cout << "Logged in as: " << currentRole << "\n";

    if (currentRole == "admin") adminMenu();
    else if (currentRole == "staff") staffMenu();
    else guestMenu();

    return 0;
}
