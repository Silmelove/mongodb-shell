# Learning Management System (LMS) - 5 Lớp Chính

## Tổng Quan
Dự án LMS được xây dựng bằng C++ với 3 lớp chính thể hiện đầy đủ các tính chất của Lập Trình Hướng Đối Tượng (OOP).

## 3 Lớp Chính

### 1. **User** (Lớp Cơ Sở)

#### User.h
```cpp
#pragma once
#include <string>
#include <vector>
#include <memory>
#include <iostream>
using namespace std;

// Forward declarations
class Course;
class Assignment;

// Base User class - Demonstrates ABSTRACTION and INHERITANCE
class User {
protected:
    string username;
    string password;
    string name;
    bool isLoggedIn;
    string userID;

public:
    // Constructor - Demonstrates ENCAPSULATION
    User(const string& username, const string& password, 
         const string& name, const string& userID);
    
    // Virtual Destructor - Demonstrates POLYMORPHISM
    virtual ~User() = default;
    
    // Pure Virtual Methods - Demonstrates ABSTRACTION
    virtual string getUserType() const = 0;
    virtual void displayMenu() = 0;
    virtual void performAction(int choice) = 0;
    
    // Authentication methods - Demonstrates ENCAPSULATION
    bool login(const string& inputPassword);
    void logout();
    bool changePassword(const string& oldPassword, const string& newPassword);
    
    // Getters - Demonstrates ENCAPSULATION
    string getUsername() const;
    string getName() const;
    string getUserID() const;
    bool getLoginStatus() const;
    
    // Setters - Demonstrates ENCAPSULATION
    void setName(const string& newName);
    
    // Utility methods - Demonstrates ENCAPSULATION
    bool verifyPassword(const string& inputPassword) const;
    void displayUserInfo() const;
    
    // Static method - Demonstrates STATIC MEMBERS
    static string generateUserID(const string& userType);
    
protected:
    // Protected method for derived classes
    void setPassword(const string& newPassword);
};
```

#### User.cpp
```cpp
#include "User.h"
#include <iostream>
#include <iomanip>
#include <ctime>
#include <sstream>
#include <random>
using namespace std;

// Constructor - Demonstrates ENCAPSULATION
User::User(const string& username, const string& password, 
           const string& name, const string& userID)
    : username(username), password(password), name(name), 
      isLoggedIn(false), userID(userID) {
}

// Authentication methods - Demonstrates ENCAPSULATION
bool User::login(const string& inputPassword) {
    if (verifyPassword(inputPassword)) {
        isLoggedIn = true;
        cout << "Login successful! Welcome, " << name << "!" << endl;
        return true;
    } else {
        cout << "Invalid password. Login failed." << endl;
        return false;
    }
}

void User::logout() {
    isLoggedIn = false;
    cout << "Logged out successfully. Goodbye, " << name << "!" << endl;
}

bool User::changePassword(const string& oldPassword, const string& newPassword) {
    if (verifyPassword(oldPassword)) {
        setPassword(newPassword);
        cout << "Password changed successfully." << endl;
        return true;
    } else {
        cout << "Old password is incorrect." << endl;
        return false;
    }
}

// Getters - Demonstrates ENCAPSULATION
string User::getUsername() const {
    return username;
}

string User::getName() const {
    return name;
}

string User::getUserID() const {
    return userID;
}

bool User::getLoginStatus() const {
    return isLoggedIn;
}

// Setters - Demonstrates ENCAPSULATION
void User::setName(const string& newName) {
    name = newName;
    cout << "Name updated successfully." << endl;
}

void User::setPassword(const string& newPassword) {
    password = newPassword;
}

// Utility methods - Demonstrates ENCAPSULATION
bool User::verifyPassword(const string& inputPassword) const {
    return password == inputPassword;
}

void User::displayUserInfo() const {
    cout << "\n=== User Information ===" << endl;
    cout << "User ID: " << userID << endl;
    cout << "Username: " << username << endl;
    cout << "Name: " << name << endl;
    cout << "Type: " << getUserType() << endl;
    cout << "Status: " << (isLoggedIn ? "Logged In" : "Logged Out") << endl;
    cout << "========================" << endl;
}

// Static method - Demonstrates STATIC MEMBERS
string User::generateUserID(const string& userType) {
    auto now = time(nullptr);
    struct tm tm;
    localtime_s(&tm, &now);
    
    ostringstream oss;
    oss << userType.substr(0, 3) << "_" 
        << put_time(&tm, "%Y%m%d_%H%M%S");
    
    // Add random number for uniqueness
    random_device rd;
    mt19937 gen(rd());
    uniform_int_distribution<> dis(1000, 9999);
    oss << "_" << dis(gen);
    
    return oss.str();
}
```

---

### 2. **Student** (Lớp Kế Thừa)

#### Student.h
```cpp
#pragma once
#include "User.h"
#include "Assignment.h"
#include <vector>
#include <memory>
#include <string>
using namespace std;

// Forward declarations
class Course;
class Submission;
class Grade;

// Student class - Demonstrates INHERITANCE and POLYMORPHISM
class Student : public User, public enable_shared_from_this<Student> {
private:
    string studentID;
    vector<shared_ptr<Course>> enrolledCourses;
    vector<shared_ptr<Submission>> submissions;
    vector<shared_ptr<Grade>> grades;
    double gpa;
    int creditsCompleted;

public:
    // Constructor - Demonstrates INHERITANCE
    Student(const string& username, const string& password, 
            const string& name, const string& studentID);
    
    // Destructor - Demonstrates POLYMORPHISM
    ~Student() override = default;
    
    // Override virtual methods - Demonstrates POLYMORPHISM
    string getUserType() const override;
    void displayMenu() override;
    void performAction(int choice) override;
    
    // Student-specific methods - Demonstrates ENCAPSULATION
    bool enrollInCourse(shared_ptr<Course> course);
    bool isEnrolledInCourse(const string& courseID) const;
    void viewEnrolledCourses() const;
    void viewCourseMaterials(const string& courseID) const;
    
    // Assignment methods - Demonstrates COMPOSITION
    shared_ptr<Submission> submitAssignment(const string& assignmentID, 
                                           const string& content);
    bool hasSubmittedAssignment(const string& assignmentID) const;
    shared_ptr<Submission> getSubmission(const string& assignmentID) const;
    
    // Grade methods - Demonstrates COMPOSITION
    void viewGrades() const;
    void viewGradeForAssignment(const string& assignmentID) const;
    void addGrade(shared_ptr<Grade> grade);
    double calculateGPA() const;
    
    // Getters - Demonstrates ENCAPSULATION
    string getStudentID() const;
    const vector<shared_ptr<Course>>& getEnrolledCourses() const;
    const vector<shared_ptr<Submission>>& getSubmissions() const;
    const vector<shared_ptr<Grade>>& getGrades() const;
    double getGPA() const;
    int getCreditsCompleted() const;
    
    // Setters - Demonstrates ENCAPSULATION
    void setGPA(double newGPA);
    void setCreditsCompleted(int credits);
    
    // Utility methods - Demonstrates ENCAPSULATION
    void addSubmission(shared_ptr<Submission> submission);
    void displayStudentInfo() const;
    string getCurrentDate() const;
    
    // Static methods - Demonstrates STATIC MEMBERS
    static shared_ptr<Student> createStudent(const string& username, 
                                           const string& password, 
                                           const string& name);
    
private:
    // Private helper methods - Demonstrates ENCAPSULATION
    void updateGPA();
    bool isValidCourse(const shared_ptr<Course>& course) const;
};
```

#### Student.cpp
```cpp
#include "Student.h"
#include "Submission.h"
#include "Course.h"
#include "Grade.h"
#include <iostream>
#include <algorithm>
#include <iomanip>
#include <ctime>
#include <sstream>
#define _CRT_SECURE_NO_WARNINGS
using namespace std;

// Constructor - Demonstrates INHERITANCE
Student::Student(const string& username, const string& password, 
                 const string& name, const string& studentID)
    : User(username, password, name, studentID), studentID(studentID), 
      gpa(0.0), creditsCompleted(0) {
}

// Override virtual methods - Demonstrates POLYMORPHISM
string Student::getUserType() const {
    return "Student";
}

void Student::displayMenu() {
    cout << "\n=== Student Dashboard ===" << endl;
    cout << "Welcome, " << getName() << " (" << studentID << ")" << endl;
    cout << "1. View Enrolled Courses" << endl;
    cout << "2. Enroll in Course" << endl;
    cout << "3. View Course Materials" << endl;
    cout << "4. View Assignments" << endl;
    cout << "5. Submit Assignment" << endl;
    cout << "6. View Grades" << endl;
    cout << "7. View Student Info" << endl;
    cout << "8. Logout" << endl;
    cout << "=========================" << endl;
}

void Student::performAction(int choice) {
    switch (choice) {
        case 1:
            viewEnrolledCourses();
            break;
        case 2:
            cout << "Course enrollment feature - to be implemented" << endl;
            break;
        case 3:
            cout << "View course materials feature - to be implemented" << endl;
            break;
        case 4:
            cout << "View assignments feature - to be implemented" << endl;
            break;
        case 5:
            cout << "Submit assignment feature - to be implemented" << endl;
            break;
        case 6:
            viewGrades();
            break;
        case 7:
            displayStudentInfo();
            break;
        case 8:
            logout();
            break;
        default:
            cout << "Invalid choice. Please try again." << endl;
            break;
    }
}

// Student-specific methods - Demonstrates ENCAPSULATION
bool Student::enrollInCourse(shared_ptr<Course> course) {
    if (course == nullptr) {
        cout << "Invalid course." << endl;
        return false;
    }
    
    // Check if already enrolled
    if (isEnrolledInCourse(course->getCourseID())) {
        cout << "You are already enrolled in course " << course->getCourseName() << "." << endl;
        return false;
    }
    
    // Add to enrolled courses
    enrolledCourses.push_back(course);
    
    // Add student to course
    course->addStudent(shared_from_this());
    
    cout << "Successfully enrolled in course " << course->getCourseName() << "." << endl;
    return true;
}

bool Student::isEnrolledInCourse(const string& courseID) const {
    return any_of(enrolledCourses.begin(), enrolledCourses.end(),
        [&courseID](const shared_ptr<Course>& course) {
            return course->getCourseID() == courseID;
        });
}

void Student::viewEnrolledCourses() const {
    cout << "\n=== Your Enrolled Courses ===" << endl;
    if (enrolledCourses.empty()) {
        cout << "You are not enrolled in any courses yet." << endl;
    } else {
        for (const auto& course : enrolledCourses) {
            course->displayCourseInfo();
        }
    }
    cout << "=============================" << endl;
}

void Student::viewCourseMaterials(const string& courseID) const {
    auto course = find_if(enrolledCourses.begin(), enrolledCourses.end(),
        [&courseID](const shared_ptr<Course>& course) {
            return course->getCourseID() == courseID;
        });
    
    if (course != enrolledCourses.end()) {
        (*course)->displayMaterials();
    } else {
        cout << "Course not found or you are not enrolled in this course." << endl;
    }
}

// Assignment methods - Demonstrates COMPOSITION
shared_ptr<Submission> Student::submitAssignment(const string& assignmentID, 
                                                 const string& content) {
    // Find the assignment in enrolled courses
    shared_ptr<Assignment> assignment = nullptr;
    shared_ptr<Course> course = nullptr;
    
    for (const auto& enrolledCourse : enrolledCourses) {
        assignment = enrolledCourse->findAssignment(assignmentID);
        if (assignment != nullptr) {
            course = enrolledCourse;
            break;
        }
    }
    
    if (assignment == nullptr) {
        cout << "Assignment not found or you are not enrolled in the course." << endl;
        return nullptr;
    }
    
    // Check if already submitted
    if (hasSubmittedAssignment(assignmentID)) {
        cout << "You have already submitted this assignment." << endl;
        return nullptr;
    }
    
    // Check deadline
    string currentDate = getCurrentDate();
    bool isLate = assignment->isSubmissionLate(currentDate);
    
    if (isLate) {
        cout << "Warning: This submission is late!" << endl;
    }
    
    // Create submission
    string submissionID = "SUB_" + assignmentID + "_" + studentID + "_" + to_string(time(nullptr));
    auto submission = make_shared<Submission>(submissionID, shared_from_this(), content, currentDate, isLate);
    
    // Add to assignment and student's submissions
    assignment->addSubmission(submission);
    submissions.push_back(submission);
    
    cout << "Assignment submitted successfully!" << endl;
    if (isLate) {
        cout << "Note: This submission was late." << endl;
    }
    
    return submission;
}

bool Student::hasSubmittedAssignment(const string& assignmentID) const {
    return any_of(submissions.begin(), submissions.end(),
        [&assignmentID](const shared_ptr<Submission>& submission) {
            return submission->getSubmissionID().find(assignmentID) != string::npos;
        });
}

shared_ptr<Submission> Student::getSubmission(const string& assignmentID) const {
    auto it = find_if(submissions.begin(), submissions.end(),
        [&assignmentID](const shared_ptr<Submission>& submission) {
            return submission->getSubmissionID().find(assignmentID) != string::npos;
        });
    
    if (it != submissions.end()) {
        return *it;
    }
    return nullptr;
}

// Grade methods - Demonstrates COMPOSITION
void Student::viewGrades() const {
    cout << "\n=== Your Grades ===" << endl;
    if (grades.empty()) {
        cout << "No grades available yet." << endl;
    } else {
        for (const auto& grade : grades) {
            grade->displayGrade();
        }
    }
    cout << "==================" << endl;
}

void Student::viewGradeForAssignment(const string& assignmentID) const {
    auto grade = find_if(grades.begin(), grades.end(),
        [&assignmentID](const shared_ptr<Grade>& grade) {
            return grade->getAssignment()->getAssignmentID() == assignmentID;
        });
    
    if (grade != grades.end()) {
        (*grade)->displayGrade();
    } else {
        cout << "No grade found for this assignment." << endl;
    }
}

void Student::addGrade(shared_ptr<Grade> grade) {
    grades.push_back(grade);
    updateGPA();
}

double Student::calculateGPA() const {
    if (grades.empty()) return 0.0;
    
    double totalPoints = 0.0;
    int totalCredits = 0;
    
    for (const auto& grade : grades) {
        totalPoints += grade->getScore() * grade->getAssignment()->getCredits();
        totalCredits += grade->getAssignment()->getCredits();
    }
    
    return totalCredits > 0 ? totalPoints / totalCredits : 0.0;
}

// Getters - Demonstrates ENCAPSULATION
string Student::getStudentID() const {
    return studentID;
}

const vector<shared_ptr<Course>>& Student::getEnrolledCourses() const {
    return enrolledCourses;
}

const vector<shared_ptr<Submission>>& Student::getSubmissions() const {
    return submissions;
}

const vector<shared_ptr<Grade>>& Student::getGrades() const {
    return grades;
}

double Student::getGPA() const {
    return gpa;
}

int Student::getCreditsCompleted() const {
    return creditsCompleted;
}

// Setters - Demonstrates ENCAPSULATION
void Student::setGPA(double newGPA) {
    gpa = newGPA;
}

void Student::setCreditsCompleted(int credits) {
    creditsCompleted = credits;
}

// Utility methods - Demonstrates ENCAPSULATION
void Student::addSubmission(shared_ptr<Submission> submission) {
    submissions.push_back(submission);
}

void Student::displayStudentInfo() const {
    cout << "\n=== Student Information ===" << endl;
    displayUserInfo();
    cout << "Student ID: " << studentID << endl;
    cout << "GPA: " << fixed << setprecision(2) << gpa << endl;
    cout << "Credits Completed: " << creditsCompleted << endl;
    cout << "Enrolled Courses: " << enrolledCourses.size() << endl;
    cout << "Submissions: " << submissions.size() << endl;
    cout << "Grades: " << grades.size() << endl;
    cout << "============================" << endl;
}

string Student::getCurrentDate() const {
    auto now = time(nullptr);
    struct tm tm;
    localtime_s(&tm, &now);
    
    ostringstream oss;
    oss << put_time(&tm, "%Y-%m-%d %H:%M:%S");
    return oss.str();
}

// Static methods - Demonstrates STATIC MEMBERS
shared_ptr<Student> Student::createStudent(const string& username, 
                                          const string& password, 
                                          const string& name) {
    string studentID = User::generateUserID("Student");
    return make_shared<Student>(username, password, name, studentID);
}

// Private helper methods - Demonstrates ENCAPSULATION
void Student::updateGPA() {
    gpa = calculateGPA();
}

bool Student::isValidCourse(const shared_ptr<Course>& course) const {
    return course != nullptr;
}
```

---

### 3. **Assignment** (Lớp Nghiệp Vụ)

#### Assignment.h
```cpp
#pragma once
#include <string>
#include <vector>
#include <memory>
#include <ctime>
using namespace std;

// Forward declaration
class Submission;

// Assignment class - Demonstrates ENCAPSULATION and COMPOSITION
class Assignment {
private:
    string assignmentID;
    string title;
    string description;
    string deadline;
    vector<shared_ptr<Submission>> submissions;
    bool isActive;
    int credits;
    double maxScore;

public:
    // Constructor - Demonstrates ENCAPSULATION
    Assignment(const string& assignmentID, const string& title, 
              const string& description, const string& deadline,
              int credits = 3, double maxScore = 100.0);
    
    // Destructor
    ~Assignment() = default;
    
    // Assignment management methods - Demonstrates ENCAPSULATION
    void addSubmission(shared_ptr<Submission> submission);
    bool isSubmissionLate(const string& submissionDate) const;
    bool isDeadlinePassed() const;
    vector<shared_ptr<Submission>> getLateSubmissions() const;
    vector<shared_ptr<Submission>> getOnTimeSubmissions() const;
    
    // Getters - Demonstrates ENCAPSULATION
    string getAssignmentID() const;
    string getTitle() const;
    string getDescription() const;
    string getDeadline() const;
    const vector<shared_ptr<Submission>>& getSubmissions() const;
    bool getIsActive() const;
    int getCredits() const;
    double getMaxScore() const;
    
    // Setters - Demonstrates ENCAPSULATION
    void setTitle(const string& newTitle);
    void setDescription(const string& newDescription);
    void setDeadline(const string& newDeadline);
    void setIsActive(bool active);
    void setCredits(int newCredits);
    void setMaxScore(double newMaxScore);
    
    // Utility methods - Demonstrates ENCAPSULATION
    void displayAssignment() const;
    void displaySubmissions() const;
    int getSubmissionCount() const;
    bool hasStudentSubmitted(const string& studentID) const;
    shared_ptr<Submission> getStudentSubmission(const string& studentID) const;
    string getCurrentDate() const;
    bool compareDates(const string& date1, const string& date2) const;
    
    // Static methods - Demonstrates STATIC MEMBERS
    static shared_ptr<Assignment> createAssignment(const string& title, 
                                                  const string& description, 
                                                  const string& deadline);
    
private:
    // Private helper methods - Demonstrates ENCAPSULATION
    bool isValidDate(const string& date) const;
    string formatDate(const string& date) const;
};
```

#### Assignment.cpp
```cpp
#include "Assignment.h"
#include "Submission.h"
#include <iostream>
#include <iomanip>
#include <algorithm>
#include <sstream>
#define _CRT_SECURE_NO_WARNINGS
using namespace std;

// Constructor - Demonstrates ENCAPSULATION
Assignment::Assignment(const string& assignmentID, const string& title, 
                      const string& description, const string& deadline,
                      int credits, double maxScore)
    : assignmentID(assignmentID), title(title), description(description), 
      deadline(deadline), isActive(true), credits(credits), maxScore(maxScore) {
}

// Assignment management methods - Demonstrates ENCAPSULATION
void Assignment::addSubmission(shared_ptr<Submission> submission) {
    if (submission == nullptr) {
        cout << "Invalid submission." << endl;
        return;
    }
    
    submissions.push_back(submission);
    cout << "Submission added successfully." << endl;
}

bool Assignment::isSubmissionLate(const string& submissionDate) const {
    return submissionDate > deadline;
}

bool Assignment::isDeadlinePassed() const {
    string currentDate = getCurrentDate();
    return currentDate > deadline;
}

vector<shared_ptr<Submission>> Assignment::getLateSubmissions() const {
    vector<shared_ptr<Submission>> lateSubmissions;
    for (const auto& submission : submissions) {
        if (submission->getIsLate()) {
            lateSubmissions.push_back(submission);
        }
    }
    return lateSubmissions;
}

vector<shared_ptr<Submission>> Assignment::getOnTimeSubmissions() const {
    vector<shared_ptr<Submission>> onTimeSubmissions;
    for (const auto& submission : submissions) {
        if (!submission->getIsLate()) {
            onTimeSubmissions.push_back(submission);
        }
    }
    return onTimeSubmissions;
}

// Getters - Demonstrates ENCAPSULATION
string Assignment::getAssignmentID() const {
    return assignmentID;
}

string Assignment::getTitle() const {
    return title;
}

string Assignment::getDescription() const {
    return description;
}

string Assignment::getDeadline() const {
    return deadline;
}

const vector<shared_ptr<Submission>>& Assignment::getSubmissions() const {
    return submissions;
}

bool Assignment::getIsActive() const {
    return isActive;
}

int Assignment::getCredits() const {
    return credits;
}

double Assignment::getMaxScore() const {
    return maxScore;
}

// Setters - Demonstrates ENCAPSULATION
void Assignment::setTitle(const string& newTitle) {
    title = newTitle;
    cout << "Assignment title updated successfully." << endl;
}

void Assignment::setDescription(const string& newDescription) {
    description = newDescription;
    cout << "Assignment description updated successfully." << endl;
}

void Assignment::setDeadline(const string& newDeadline) {
    deadline = newDeadline;
    cout << "Assignment deadline updated successfully." << endl;
}

void Assignment::setIsActive(bool active) {
    isActive = active;
    cout << "Assignment status updated successfully." << endl;
}

void Assignment::setCredits(int newCredits) {
    credits = newCredits;
    cout << "Assignment credits updated successfully." << endl;
}

void Assignment::setMaxScore(double newMaxScore) {
    maxScore = newMaxScore;
    cout << "Assignment max score updated successfully." << endl;
}

// Utility methods - Demonstrates ENCAPSULATION
void Assignment::displayAssignment() const {
    cout << "\n=== Assignment Information ===" << endl;
    cout << "Assignment ID: " << assignmentID << endl;
    cout << "Title: " << title << endl;
    cout << "Description: " << description << endl;
    cout << "Deadline: " << deadline << endl;
    cout << "Credits: " << credits << endl;
    cout << "Max Score: " << maxScore << endl;
    cout << "Status: " << (isActive ? "Active" : "Inactive") << endl;
    cout << "Submissions: " << submissions.size() << endl;
    cout << "Deadline Passed: " << (isDeadlinePassed() ? "Yes" : "No") << endl;
    cout << "=============================" << endl;
}

void Assignment::displaySubmissions() const {
    cout << "\n=== Submissions for " << title << " ===" << endl;
    if (submissions.empty()) {
        cout << "No submissions yet." << endl;
    } else {
        for (const auto& submission : submissions) {
            submission->displaySubmission();
        }
    }
    cout << "=============================" << endl;
}

int Assignment::getSubmissionCount() const {
    return submissions.size();
}

bool Assignment::hasStudentSubmitted(const string& studentID) const {
    return any_of(submissions.begin(), submissions.end(),
        [&studentID](const shared_ptr<Submission>& submission) {
            return submission->getStudent()->getStudentID() == studentID;
        });
}

shared_ptr<Submission> Assignment::getStudentSubmission(const string& studentID) const {
    auto it = find_if(submissions.begin(), submissions.end(),
        [&studentID](const shared_ptr<Submission>& submission) {
            return submission->getStudent()->getStudentID() == studentID;
        });
    
    if (it != submissions.end()) {
        return *it;
    }
    return nullptr;
}

string Assignment::getCurrentDate() const {
    auto now = time(nullptr);
    struct tm tm;
    localtime_s(&tm, &now);
    
    ostringstream oss;
    oss << put_time(&tm, "%Y-%m-%d %H:%M:%S");
    return oss.str();
}

bool Assignment::compareDates(const string& date1, const string& date2) const {
    // Simple string comparison for dates in YYYY-MM-DD HH:MM:SS format
    return date1 > date2;
}

// Static methods - Demonstrates STATIC MEMBERS
shared_ptr<Assignment> Assignment::createAssignment(const string& title, 
                                                   const string& description, 
                                                   const string& deadline) {
    string assignmentID = "ASS_" + to_string(time(nullptr));
    return make_shared<Assignment>(assignmentID, title, description, deadline);
}

// Private helper methods - Demonstrates ENCAPSULATION
bool Assignment::isValidDate(const string& date) const {
    // Simple validation - in real implementation, use proper date parsing
    return date.length() >= 10; // At least YYYY-MM-DD
}

string Assignment::formatDate(const string& date) const {
    // Simple formatting - in real implementation, use proper date formatting
    return date;
}
```

---

### 4. **Submission** (Lớp Bài Nộp)

#### Submission.h
```cpp
#pragma once
#include "Student.h"
#include <string>
#include <memory>
using namespace std;

// Forward declaration
class Student;

// Submission class - Demonstrates COMPOSITION and ENCAPSULATION
class Submission {
private:
    string submissionID;
    shared_ptr<Student> student;
    string content;
    string submittedDate;
    bool isLate;
    bool isGraded;
    double score;
    string feedback;

public:
    // Constructor - Demonstrates ENCAPSULATION
    Submission(const string& submissionID, shared_ptr<Student> student, 
              const string& content, const string& submittedDate, 
              bool isLate = false);
    
    // Destructor
    ~Submission() = default;
    
    // Getters - Demonstrates ENCAPSULATION
    string getSubmissionID() const;
    shared_ptr<Student> getStudent() const;
    string getContent() const;
    string getSubmittedDate() const;
    bool getIsLate() const;
    bool getIsGraded() const;
    double getScore() const;
    string getFeedback() const;
    
    // Setters - Demonstrates ENCAPSULATION
    void setContent(const string& newContent);
    void setIsLate(bool late);
    void setIsGraded(bool graded);
    void setScore(double newScore);
    void setFeedback(const string& newFeedback);
    
    // Utility methods - Demonstrates ENCAPSULATION
    void displaySubmission() const;
    string getCurrentDate() const;
    bool isSubmissionLate(const string& deadline) const;
    void gradeSubmission(double score, const string& feedback = "");
    
    // Static methods - Demonstrates STATIC MEMBERS
    static shared_ptr<Submission> createSubmission(shared_ptr<Student> student, 
                                                 const string& content);
    
private:
    // Private helper methods - Demonstrates ENCAPSULATION
    string generateSubmissionID() const;
    bool isValidScore(double score) const;
};
```

#### Submission.cpp
```cpp
#include "Submission.h"
#include "Student.h"
#include <iostream>
#include <iomanip>
#include <ctime>
#include <sstream>
#define _CRT_SECURE_NO_WARNINGS
using namespace std;

// Constructor - Demonstrates ENCAPSULATION
Submission::Submission(const string& submissionID, shared_ptr<Student> student, 
                      const string& content, const string& submittedDate, 
                      bool isLate)
    : submissionID(submissionID), student(student), content(content), 
      submittedDate(submittedDate), isLate(isLate), isGraded(false), 
      score(0.0), feedback("") {
}

// Getters - Demonstrates ENCAPSULATION
string Submission::getSubmissionID() const {
    return submissionID;
}

shared_ptr<Student> Submission::getStudent() const {
    return student;
}

string Submission::getContent() const {
    return content;
}

string Submission::getSubmittedDate() const {
    return submittedDate;
}

bool Submission::getIsLate() const {
    return isLate;
}

bool Submission::getIsGraded() const {
    return isGraded;
}

double Submission::getScore() const {
    return score;
}

string Submission::getFeedback() const {
    return feedback;
}

// Setters - Demonstrates ENCAPSULATION
void Submission::setContent(const string& newContent) {
    content = newContent;
    cout << "Submission content updated successfully." << endl;
}

void Submission::setIsLate(bool late) {
    isLate = late;
    cout << "Submission late status updated." << endl;
}

void Submission::setIsGraded(bool graded) {
    isGraded = graded;
    cout << "Submission graded status updated." << endl;
}

void Submission::setScore(double newScore) {
    if (isValidScore(newScore)) {
        score = newScore;
        cout << "Submission score updated successfully." << endl;
    } else {
        cout << "Invalid score. Score must be between 0 and 100." << endl;
    }
}

void Submission::setFeedback(const string& newFeedback) {
    feedback = newFeedback;
    cout << "Submission feedback updated successfully." << endl;
}

// Utility methods - Demonstrates ENCAPSULATION
void Submission::displaySubmission() const {
    cout << "\n=== Submission Information ===" << endl;
    cout << "Submission ID: " << submissionID << endl;
    cout << "Student: " << student->getName() << " (" << student->getStudentID() << ")" << endl;
    cout << "Content: " << content << endl;
    cout << "Submitted Date: " << submittedDate << endl;
    cout << "Is Late: " << (isLate ? "Yes" : "No") << endl;
    cout << "Is Graded: " << (isGraded ? "Yes" : "No") << endl;
    if (isGraded) {
        cout << "Score: " << fixed << setprecision(2) << score << endl;
        cout << "Feedback: " << feedback << endl;
    }
    cout << "===============================" << endl;
}

string Submission::getCurrentDate() const {
    auto now = time(nullptr);
    struct tm tm;
    localtime_s(&tm, &now);
    
    ostringstream oss;
    oss << put_time(&tm, "%Y-%m-%d %H:%M:%S");
    return oss.str();
}

bool Submission::isSubmissionLate(const string& deadline) const {
    return submittedDate > deadline;
}

void Submission::gradeSubmission(double score, const string& feedback) {
    if (isValidScore(score)) {
        this->score = score;
        this->feedback = feedback;
        this->isGraded = true;
        cout << "Submission graded successfully!" << endl;
    } else {
        cout << "Invalid score. Score must be between 0 and 100." << endl;
    }
}

// Static methods - Demonstrates STATIC MEMBERS
shared_ptr<Submission> Submission::createSubmission(shared_ptr<Student> student, 
                                                   const string& content) {
    if (student == nullptr) {
        cout << "Invalid student." << endl;
        return nullptr;
    }
    
    string submissionID = "SUB_" + to_string(time(nullptr));
    string currentDate = "";
    
    auto now = time(nullptr);
    struct tm tm;
    localtime_s(&tm, &now);
    
    ostringstream oss;
    oss << put_time(&tm, "%Y-%m-%d %H:%M:%S");
    currentDate = oss.str();
    
    return make_shared<Submission>(submissionID, student, content, currentDate);
}

// Private helper methods - Demonstrates ENCAPSULATION
string Submission::generateSubmissionID() const {
    return "SUB_" + to_string(time(nullptr));
}

bool Submission::isValidScore(double score) const {
    return score >= 0.0 && score <= 100.0;
}
```

---

### 5. **Grade** (Lớp Điểm Số)

#### Grade.h
```cpp
#pragma once
#include "Assignment.h"
#include "Student.h"
#include "Submission.h"
#include <string>
#include <memory>
using namespace std;

// Forward declarations
class Assignment;
class Student;
class Submission;

// Grade class - Demonstrates COMPOSITION and ENCAPSULATION
class Grade {
private:
    string gradeID;
    shared_ptr<Assignment> assignment;
    shared_ptr<Student> student;
    shared_ptr<Submission> submission;
    double score;
    string feedback;
    string gradedDate;
    string gradedBy;

public:
    // Constructor - Demonstrates ENCAPSULATION
    Grade(const string& gradeID, shared_ptr<Assignment> assignment, 
          shared_ptr<Student> student, shared_ptr<Submission> submission,
          double score, const string& feedback = "", 
          const string& gradedBy = "Teacher");
    
    // Destructor
    ~Grade() = default;
    
    // Getters - Demonstrates ENCAPSULATION
    string getGradeID() const;
    shared_ptr<Assignment> getAssignment() const;
    shared_ptr<Student> getStudent() const;
    shared_ptr<Submission> getSubmission() const;
    double getScore() const;
    string getFeedback() const;
    string getGradedDate() const;
    string getGradedBy() const;
    
    // Setters - Demonstrates ENCAPSULATION
    void setScore(double newScore);
    void setFeedback(const string& newFeedback);
    void setGradedBy(const string& newGradedBy);
    
    // Utility methods - Demonstrates ENCAPSULATION
    void displayGrade() const;
    string getCurrentDate() const;
    string getLetterGrade() const;
    bool isPassingGrade() const;
    void updateGrade(double newScore, const string& newFeedback = "");
    
    // Static methods - Demonstrates STATIC MEMBERS
    static shared_ptr<Grade> createGrade(shared_ptr<Assignment> assignment, 
                                        shared_ptr<Student> student, 
                                        shared_ptr<Submission> submission,
                                        double score, const string& feedback = "");
    
private:
    // Private helper methods - Demonstrates ENCAPSULATION
    string generateGradeID() const;
    bool isValidScore(double score) const;
    string calculateLetterGrade(double score) const;
};
```

#### Grade.cpp
```cpp
#include "Grade.h"
#include "Assignment.h"
#include "Student.h"
#include "Submission.h"
#include <iostream>
#include <iomanip>
#include <ctime>
#include <sstream>
#define _CRT_SECURE_NO_WARNINGS
using namespace std;

// Constructor - Demonstrates ENCAPSULATION
Grade::Grade(const string& gradeID, shared_ptr<Assignment> assignment, 
            shared_ptr<Student> student, shared_ptr<Submission> submission,
            double score, const string& feedback, const string& gradedBy)
    : gradeID(gradeID), assignment(assignment), student(student), 
      submission(submission), score(score), feedback(feedback), 
      gradedBy(gradedBy) {
    
    // Set graded date to current date
    gradedDate = getCurrentDate();
}

// Getters - Demonstrates ENCAPSULATION
string Grade::getGradeID() const {
    return gradeID;
}

shared_ptr<Assignment> Grade::getAssignment() const {
    return assignment;
}

shared_ptr<Student> Grade::getStudent() const {
    return student;
}

shared_ptr<Submission> Grade::getSubmission() const {
    return submission;
}

double Grade::getScore() const {
    return score;
}

string Grade::getFeedback() const {
    return feedback;
}

string Grade::getGradedDate() const {
    return gradedDate;
}

string Grade::getGradedBy() const {
    return gradedBy;
}

// Setters - Demonstrates ENCAPSULATION
void Grade::setScore(double newScore) {
    if (isValidScore(newScore)) {
        score = newScore;
        cout << "Grade score updated successfully." << endl;
    } else {
        cout << "Invalid score. Score must be between 0 and 100." << endl;
    }
}

void Grade::setFeedback(const string& newFeedback) {
    feedback = newFeedback;
    cout << "Grade feedback updated successfully." << endl;
}

void Grade::setGradedBy(const string& newGradedBy) {
    gradedBy = newGradedBy;
    cout << "Graded by updated successfully." << endl;
}

// Utility methods - Demonstrates ENCAPSULATION
void Grade::displayGrade() const {
    cout << "\n=== Grade Information ===" << endl;
    cout << "Grade ID: " << gradeID << endl;
    cout << "Assignment: " << assignment->getTitle() << endl;
    cout << "Student: " << student->getName() << " (" << student->getStudentID() << ")" << endl;
    cout << "Score: " << fixed << setprecision(2) << score << "/" << assignment->getMaxScore() << endl;
    cout << "Letter Grade: " << getLetterGrade() << endl;
    cout << "Feedback: " << feedback << endl;
    cout << "Graded Date: " << gradedDate << endl;
    cout << "Graded By: " << gradedBy << endl;
    cout << "Status: " << (isPassingGrade() ? "PASS" : "FAIL") << endl;
    cout << "=========================" << endl;
}

string Grade::getCurrentDate() const {
    auto now = time(nullptr);
    struct tm tm;
    localtime_s(&tm, &now);
    
    ostringstream oss;
    oss << put_time(&tm, "%Y-%m-%d %H:%M:%S");
    return oss.str();
}

string Grade::getLetterGrade() const {
    return calculateLetterGrade(score);
}

bool Grade::isPassingGrade() const {
    return score >= 50.0; // Passing grade is 50%
}

void Grade::updateGrade(double newScore, const string& newFeedback) {
    if (isValidScore(newScore)) {
        score = newScore;
        feedback = newFeedback;
        gradedDate = getCurrentDate();
        cout << "Grade updated successfully!" << endl;
    } else {
        cout << "Invalid score. Score must be between 0 and 100." << endl;
    }
}

// Static methods - Demonstrates STATIC MEMBERS
shared_ptr<Grade> Grade::createGrade(shared_ptr<Assignment> assignment, 
                                    shared_ptr<Student> student, 
                                    shared_ptr<Submission> submission,
                                    double score, const string& feedback) {
    if (assignment == nullptr || student == nullptr || submission == nullptr) {
        cout << "Invalid assignment, student, or submission." << endl;
        return nullptr;
    }
    
    string gradeID = "GRD_" + to_string(time(nullptr));
    return make_shared<Grade>(gradeID, assignment, student, submission, score, feedback);
}

// Private helper methods - Demonstrates ENCAPSULATION
string Grade::generateGradeID() const {
    return "GRD_" + to_string(time(nullptr));
}

bool Grade::isValidScore(double score) const {
    return score >= 0.0 && score <= 100.0;
}

string Grade::calculateLetterGrade(double score) const {
    if (score >= 90) return "A";
    else if (score >= 80) return "B";
    else if (score >= 70) return "C";
    else if (score >= 60) return "D";
    else return "F";
}
```

---

## Demo Main Function

#### main.cpp
```cpp
#include "User.h"
#include "Student.h"
#include "Assignment.h"
#include "Submission.h"
#include "Grade.h"
#include <iostream>
#include <memory>
using namespace std;

int main() {
    cout << "=== Learning Management System Demo ===" << endl;
    cout << "Demonstrating 5 main classes with OOP principles" << endl;
    cout << "================================================" << endl;
    
    // 1. Create Student using static factory method
    cout << "\n1. Creating Student..." << endl;
    auto student = Student::createStudent("student1", "pass123", "Nguyen Van A");
    student->displayStudentInfo();
    
    // 2. Create Assignment using static factory method
    cout << "\n2. Creating Assignment..." << endl;
    auto assignment = Assignment::createAssignment("Bai Tap 1", 
                                                   "Giai bai tap ve OOP", 
                                                   "2024-12-31 23:59:59");
    assignment->displayAssignment();
    
    // 3. Student login
    cout << "\n3. Student Login..." << endl;
    student->login("pass123");
    
    // 4. Create Submission
    cout << "\n4. Creating Submission..." << endl;
    auto submission = Submission::createSubmission(student, "Day la bai lam cua toi");
    if (submission) {
        submission->displaySubmission();
    }
    
    // 5. Student submit assignment
    cout << "\n5. Student Submit Assignment..." << endl;
    auto submittedWork = student->submitAssignment(assignment->getAssignmentID(), 
                                                  "Day la bai lam cua toi");
    
    // 6. Create Grade
    cout << "\n6. Creating Grade..." << endl;
    auto grade = Grade::createGrade(assignment, student, submittedWork, 85.5, 
                                   "Bai lam tot, can cai thien them");
    if (grade) {
        grade->displayGrade();
        student->addGrade(grade);
    }
    
    // 7. Display results
    cout << "\n7. Display Results..." << endl;
    assignment->displaySubmissions();
    student->viewGrades();
    student->displayStudentInfo();
    
    // 8. Demonstrate polymorphism
    cout << "\n8. Demonstrating Polymorphism..." << endl;
    User* user = student.get();
    cout << "User type: " << user->getUserType() << endl;
    user->displayMenu();
    
    // 9. Demonstrate composition relationships
    cout << "\n9. Demonstrating Composition..." << endl;
    cout << "Student has " << student->getSubmissions().size() << " submissions" << endl;
    cout << "Student has " << student->getGrades().size() << " grades" << endl;
    cout << "Assignment has " << assignment->getSubmissionCount() << " submissions" << endl;
    
    // 10. Demonstrate encapsulation
    cout << "\n10. Demonstrating Encapsulation..." << endl;
    cout << "Student GPA: " << student->getGPA() << endl;
    cout << "Assignment Max Score: " << assignment->getMaxScore() << endl;
    cout << "Submission Score: " << submission->getScore() << endl;
    
    cout << "\n=== Demo Completed Successfully ===" << endl;
    cout << "All 5 classes demonstrated with OOP principles!" << endl;
    return 0;
}
```

---

## Hướng Dẫn Chạy Trên Visual Studio 2022

### Bước 1: Tạo Project
1. Mở Visual Studio 2022
2. Chọn **Create a new project**
3. Chọn **Console App** với **C++**
4. Đặt tên project: `LMS_Demo`
5. Chọn **C++17** hoặc **C++20** standard

### Bước 2: Thêm Files
1. Tạo các file header: `User.h`, `Student.h`, `Assignment.h`
2. Tạo các file source: `User.cpp`, `Student.cpp`, `Assignment.cpp`
3. Copy code từ README.md vào các file tương ứng
4. Thêm file `main.cpp` với demo code

### Bước 3: Cấu Hình Project
1. Right-click project → **Properties**
2. **C/C++** → **General** → **C++ Language Standard**: C++17
3. **C/C++** → **Preprocessor** → **Preprocessor Definitions**: `_CRT_SECURE_NO_WARNINGS`

### Bước 4: Build và Run
1. Nhấn **Ctrl + Shift + B** để build
2. Nhấn **F5** để chạy với debug
3. Hoặc **Ctrl + F5** để chạy không debug

### Bước 5: Test Demo
1. Chạy program sẽ hiển thị demo sequence
2. Kiểm tra output để đảm bảo các tính chất OOP hoạt động
3. Test các chức năng: login, submit assignment, display info

---

## Định Nghĩa Tính Chất OOP

### 1. **Encapsulation (Đóng Gói)**
**Định nghĩa**: Encapsulation là khả năng đóng gói dữ liệu và các phương thức liên quan vào trong một đơn vị duy nhất (class), đồng thời kiểm soát quyền truy cập vào các thành phần đó.

**Đặc điểm**:
- **Data Hiding**: Ẩn dữ liệu bên trong class khỏi bên ngoài
- **Access Control**: Sử dụng access modifiers (private, protected, public)
- **Interface**: Cung cấp interface công khai để tương tác với object

**Trong code**:
```cpp
class User {
private:        // Ẩn dữ liệu
    string password;
    bool isLoggedIn;
    
protected:      // Chỉ lớp con truy cập được
    string userID;
    
public:         // Interface công khai
    bool login(const string& inputPassword);
    void logout();
};
```

### 2. **Inheritance (Kế Thừa)**
**Định nghĩa**: Inheritance cho phép một class (lớp con) kế thừa các thuộc tính và phương thức từ một class khác (lớp cha), tạo ra mối quan hệ "is-a".

**Đặc điểm**:
- **Code Reusability**: Tái sử dụng code từ lớp cha
- **Hierarchy**: Tạo cấu trúc phân cấp
- **Override**: Lớp con có thể ghi đè phương thức của lớp cha

**Trong code**:
```cpp
class User {                    // Lớp cha
    virtual string getUserType() const = 0;
};

class Student : public User {   // Lớp con kế thừa từ User
    string getUserType() const override {
        return "Student";       // Ghi đè phương thức
    }
};
```

### 3. **Polymorphism (Đa Hình)**
**Định nghĩa**: Polymorphism cho phép các đối tượng thuộc các lớp khác nhau có thể được xử lý thông qua cùng một interface, nhưng hành vi cụ thể sẽ khác nhau tùy thuộc vào loại đối tượng thực tế.

**Đặc điểm**:
- **Runtime Polymorphism**: Quyết định phương thức nào được gọi tại runtime
- **Virtual Functions**: Sử dụng virtual keyword
- **Dynamic Binding**: Liên kết động tại thời điểm chạy

**Trong code**:
```cpp
User* user = new Student();     // Con trỏ lớp cha trỏ đến đối tượng lớp con
user->getUserType();            // Gọi phương thức của Student, không phải User
user->displayMenu();           // Hiển thị menu của Student
```

### 4. **Abstraction (Trừu Tượng)**
**Định nghĩa**: Abstraction là quá trình ẩn các chi tiết phức tạp của implementation và chỉ hiển thị những tính năng cần thiết cho người dùng.

**Đặc điểm**:
- **Abstract Classes**: Lớp trừu tượng không thể tạo instance
- **Pure Virtual Functions**: Phương thức thuần ảo phải được implement
- **Interface**: Định nghĩa contract mà các lớp con phải tuân theo

**Trong code**:
```cpp
class User {
public:
    // Pure virtual function - lớp con PHẢI implement
    virtual string getUserType() const = 0;
    virtual void displayMenu() = 0;
    
    // Không thể tạo User user; vì User là abstract class
};
```

### 5. **Composition (Tổ Hợp)**
**Định nghĩa**: Composition là mối quan hệ "has-a" giữa các class, trong đó một class chứa các đối tượng của class khác như là thành phần của nó.

**Đặc điểm**:
- **Has-A Relationship**: Một đối tượng "có" đối tượng khác
- **Ownership**: Đối tượng chứa sở hữu đối tượng được chứa
- **Lifecycle**: Đối tượng được chứa tồn tại cùng với đối tượng chứa

**Trong code**:
```cpp
class Student {
private:
    vector<shared_ptr<Course>> enrolledCourses;    // Student HAS courses
    vector<shared_ptr<Submission>> submissions;   // Student HAS submissions
    vector<shared_ptr<Grade>> grades;            // Student HAS grades
};

class Assignment {
private:
    vector<shared_ptr<Submission>> submissions;   // Assignment HAS submissions
};
```

---

## Tính Chất OOP Được Thể Hiện Trong Code

### 1. **Encapsulation (Đóng Gói)**
- Thuộc tính private/protected
- Phương thức public để truy cập
- Ẩn implementation details

### 2. **Inheritance (Kế Thừa)**
- Student kế thừa từ User
- Override virtual methods
- Sử dụng protected members

### 3. **Polymorphism (Đa Hình)**
- Virtual methods và virtual destructor
- Override keyword
- Runtime polymorphism

### 4. **Abstraction (Trừu Tượng)**
- Pure virtual methods trong User
- Interface rõ ràng
- Ẩn complexity

### 5. **Composition (Tổ Hợp)**
- Student chứa vector của Course, Submission, Grade
- Assignment chứa vector của Submission
- Quan hệ "has-a"

---

## Cấu Trúc File

```
Project_LMS/
├── User.h              # Lớp User (Base class)
├── User.cpp
├── Student.h           # Lớp Student (Derived class)
├── Student.cpp
├── Assignment.h        # Lớp Assignment (Business logic)
├── Assignment.cpp
├── Submission.h        # Lớp Submission (Composition)
├── Submission.cpp
├── Grade.h            # Lớp Grade (Composition)
├── Grade.cpp
├── Project_LMS.cpp    # Main function
├── Project_LMS.sln    # Solution file
└── Project_LMS.vcxproj # Project file
```

---

## Kết Luận

5 lớp chính này thể hiện đầy đủ các tính chất OOP:
- **User**: Abstraction và Inheritance
- **Student**: Polymorphism và Composition  
- **Assignment**: Encapsulation và Business Logic
- **Submission**: Composition và Data Management
- **Grade**: Composition và Grade Calculation

Dự án này phù hợp để demo và học tập các khái niệm OOP cơ bản trong C++.
