#include<iostream>
#include<fstream>
#include<string>
using namespace std;

struct Student {
    int rollno;
    char name[50];
    float marks;
    char branch[30];
};

void addStudent() {
    Student s;
    ofstream file("students.dat", ios::binary | ios::app);

    cout << "\nEnter Roll Number: ";
    cin >> s.rollno;
    cout << "Enter Name: ";
    cin.ignore();
    cin.getline(s.name, 50);
    cout << "Enter Marks: ";
    cin >> s.marks;
    cout << "Enter Branch: ";
    cin.ignore();
    cin.getline(s.branch, 30);

    file.write((char*)&s, sizeof(s));
    file.close();

    cout << "\nStudent added successfully!\n";
}

void displayAll() {
    Student s;
    ifstream file("students.dat", ios::binary);

    if(!file) {
        cout << "\nNo records found!\n";
        return;
    }

    cout << "\n--- All Students ---\n";
    cout << "Roll\tName\t\tMarks\tBranch\n";
    cout << "------------------------------------\n";

    while(file.read((char*)&s, sizeof(s))) {
        cout << s.rollno << "\t" << s.name << "\t\t" << s.marks << "\t" << s.branch << "\n";
    }
    file.close();
}

void searchStudent(int roll) {
    Student s;
    ifstream file("students.dat", ios::binary);
    bool found = false;

    while(file.read((char*)&s, sizeof(s))) {
        if(s.rollno == roll) {
            cout << "\n--- Student Found ---\n";
            cout << "Roll No: " << s.rollno << "\n";
            cout << "Name: " << s.name << "\n";
            cout << "Marks: " << s.marks << "\n";
            cout << "Branch: " << s.branch << "\n";
            found = true;
            break;
        }
    }
    file.close();

    if(!found)
        cout << "\nStudent not found!\n";
}

void deleteStudent(int roll) {
    Student s;
    ifstream file("students.dat", ios::binary);
    ofstream temp("temp.dat", ios::binary);
    bool found = false;

    while(file.read((char*)&s, sizeof(s))) {
        if(s.rollno != roll) {
            temp.write((char*)&s, sizeof(s));
        } else {
            found = true;
        }
    }

    file.close();
    temp.close();

    remove("students.dat");
    rename("temp.dat", "students.dat");

    if(found)
        cout << "\nStudent deleted!\n";
    else
        cout << "\nStudent not found!\n";
}

void updateStudent(int roll) {
    Student s;
    fstream file("students.dat", ios::binary | ios::in | ios::out);
    bool found = false;

    while(file.read((char*)&s, sizeof(s))) {
        if(s.rollno == roll) {
            cout << "\nEnter new name: ";
            cin.ignore();
            cin.getline(s.name, 50);
            cout << "Enter new marks: ";
            cin >> s.marks;
            cout << "Enter new branch: ";
            cin.ignore();
            cin.getline(s.branch, 30);

            // go back to overwrite
            int pos = file.tellg();
            file.seekp(pos - sizeof(s));
            file.write((char*)&s, sizeof(s));
            found = true;
            break;
        }
    }
    file.close();

    if(found)
        cout << "\nRecord updated!\n";
    else
        cout << "\nStudent not found!\n";
}

int main() {
    int choice, roll;

    cout << "=== Student Management System ===\n";

    do {
        cout << "\n1. Add Student";
        cout << "\n2. Display All";
        cout << "\n3. Search Student";
        cout << "\n4. Update Student";
        cout << "\n5. Delete Student";
        cout << "\n6. Exit";
        cout << "\n\nEnter your choice: ";
        cin >> choice;

        switch(choice) {
            case 1:
                addStudent();
                break;
            case 2:
                displayAll();
                break;
            case 3:
                cout << "Enter Roll No to search: ";
                cin >> roll;
                searchStudent(roll);
                break;
            case 4:
                cout << "Enter Roll No to update: ";
                cin >> roll;
                updateStudent(roll);
                break;
            case 5:
                cout << "Enter Roll No to delete: ";
                cin >> roll;
                deleteStudent(roll);
                break;
            case 6:
                cout << "\nExiting... Goodbye!\n";
                break;
            default:
                cout << "\nInvalid choice!\n";
        }
    } while(choice != 6);

    return 0;
}
