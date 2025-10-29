# Learning Management System (LMS) - 5 Lớp Chính

## Tổng Quan
Dự án LMS được xây dựng bằng C++ với 5 lớp chính thể hiện đầy đủ các tính chất của Lập Trình Hướng Đối Tượng (OOP).

## Thứ Tự Viết Code

Khi viết code cho hệ thống LMS này, bạn nên viết theo **thứ tự phụ thuộc** từ lớp cơ sở đến lớp phức tạp:

### **1. User (Lớp Cơ Sở)** - Viết đầu tiên
### **2. Assignment (Lớp Nghiệp Vụ)** - Viết thứ hai  
### **3. Student (Lớp Kế Thừa)** - Viết thứ ba
### **4. Submission (Lớp Bài Nộp)** - Viết thứ tư
### **5. Grade (Lớp Điểm Số)** - Viết cuối cùng

## 5 Lớp Chính

### 1. **User** (Lớp Cơ Sở)
Class này thể hiện tính Encapsulation – hay đóng gói dữ liệu.
Chúng ta ẩn thông tin nhạy cảm như username và password bằng cách khai báo protected và chỉ cho lớp con truy cập như lớp Student.
Việc truy cập hoặc thay đổi dữ liệu chỉ được thực hiện thông qua các phương thức getter và setter.
Ngoài ra, User còn là một lớp trừu tượng về người dùng nói chung, nên nó cũng thể hiện Abstraction — chỉ mô tả những gì cần thiết, chưa cụ thể hóa.
#### User.h
```cpp
#pragma once
#include <string>
#include <iostream>
using namespace std;

/*
===========================================================
👨‍🏫 Giải thích mở đầu:
Đây là lớp User — đại diện cho một người dùng trong hệ thống.
Mục tiêu là thể hiện 3 tính chất của OOP:
1️⃣ Encapsulation (Đóng gói)
2️⃣ Abstraction (Trừu tượng)
3️⃣ Polymorphism (Đa hình)
===========================================================
*/

class User {
protected:
    // 🧱 Các thuộc tính này được đặt là protected — 
    // nghĩa là lớp con (như Student, Teacher) có thể truy cập, 
    // nhưng bên ngoài thì không thể.
    // Đây là một ví dụ rõ về **Encapsulation (đóng gói)**.
    string username;
    string password;
    string name;
    bool isLoggedIn;

public:
    /*
    📌 Constructor khởi tạo User
    - Dùng để truyền vào thông tin ban đầu cho người dùng.
    - Cũng là phần mở đầu thể hiện Encapsulation, vì dữ liệu được
      khởi tạo thông qua constructor thay vì truy cập trực tiếp.
    */
    User(const string& username, const string& password, const string& name);

    // 💣 Destructor ảo (virtual destructor)
    // Giúp tránh lỗi khi delete con trỏ lớp cha trỏ đến đối tượng lớp con.
    virtual ~User() = default;

    /*
    ===========================================
    🧩 PHẦN 1: ENCAPSULATION - Đóng gói
    ===========================================
    */
    // 📥 login() kiểm tra mật khẩu nhưng KHÔNG để lộ password.
    // => Thể hiện tính đóng gói dữ liệu: password được bảo vệ bên trong class.
    bool login(const string& inputPassword);
    void logout();

    // Getter — chỉ đọc dữ liệu, không thể sửa trực tiếp
    string getUsername() const;
    string getName() const;
    bool getLoginStatus() const;

    // Setter — cho phép cập nhật có kiểm soát
    void setPassword(const string& newPassword);
    void setName(const string& newName);

    /*
    ===========================================
    🧩 PHẦN 2: POLYMORPHISM & ABSTRACTION
    ===========================================
    */
    // 🌀 Hai hàm thuần ảo (pure virtual) => class này trở thành abstract class.
    // 👉 Điều này thể hiện **Abstraction (Trừu tượng)**:
    //     - User chỉ định nghĩa giao diện chung (interface)
    //     - Còn hành vi cụ thể (ví dụ: menu của Student hay Teacher)
    //       sẽ do lớp con định nghĩa.
    //
    // Đồng thời, nó thể hiện **Polymorphism (Đa hình)**:
    //     - Vì khi ta gọi user->displayMenu(),
    //       chương trình sẽ tự động chọn đúng menu theo loại User.
    // Dòng đầu tiên virtual string getUserType() const = 0; yêu cầu các lớp con phải tự định nghĩa loại người dùng của mình, ví dụ Student, Teacher hoặc Admin
     virtual string getUserType() const = 0;
    // Dòng thứ hai virtual void displayMenu() = 0; là hàm hiển thị menu riêng cho từng loại người dùng.
     // tức là chỉ nêu ra “cái gì cần làm”, còn “làm như thế nào” để lớp con tự quyết định.
    virtual void displayMenu() = 0;

    /*
    ===========================================
    🧩 PHẦN 3: HÀM TIỆN ÍCH (UTILITY)
    ===========================================
    */
    // Hàm này giúp kiểm tra password bên trong class.
    // Nó KHÔNG public ra ngoài — đây cũng là Encapsulation.
    bool verifyPassword(const string& inputPassword) const;
};

```

#### User.cpp
```cpp
#include "User.h"
#include <iostream>
using namespace std;

/*
====================================================
👨‍🏫 Giải thích mở đầu:
File này triển khai (implementation) cho các hàm trong User.
Nó tập trung thể hiện rõ tính **Encapsulation (đóng gói)** —
bằng cách chỉ thao tác với dữ liệu thông qua hàm thành viên,
chứ không bao giờ truy cập trực tiếp từ bên ngoài.
====================================================
*/

// 🧱 Constructor: khởi tạo các giá trị cho user
User::User(const string& username, const string& password, const string& name)
    : username(username), password(password), name(name), isLoggedIn(false) {}

/*
--------------------------------------------------
Hàm login(): kiểm tra mật khẩu mà không để lộ password
- Người dùng chỉ cung cấp mật khẩu nhập vào,
  hệ thống tự kiểm tra thông qua verifyPassword().
=> Đây là ví dụ điển hình của **Encapsulation**:
   dữ liệu nhạy cảm được bảo vệ, chỉ có hàm nội bộ được phép truy cập.
--------------------------------------------------
*/
bool User::login(const string& inputPassword) {
    if (verifyPassword(inputPassword)) {
        isLoggedIn = true;
        cout << "Login successful! Welcome, " << name << "!\n";
        return true;
    }
    cout << "Invalid password.\n";
    return false;
}

/*
--------------------------------------------------
Hàm logout(): thay đổi trạng thái đăng nhập
--------------------------------------------------
*/
void User::logout() {
    isLoggedIn = false;
    cout << "Logged out. Goodbye, " << name << "!\n";
}

/*
--------------------------------------------------
Các hàm getter: chỉ đọc thông tin
=> vẫn là một phần của **Encapsulation**,
   vì không ai được truy cập trực tiếp vào biến private/protected.
--------------------------------------------------
*/
string User::getUsername() const { return username; }
string User::getName() const { return name; }
bool User::getLoginStatus() const { return isLoggedIn; }

/*
--------------------------------------------------
Các hàm setter: thay đổi dữ liệu có kiểm soát
=> Ví dụ, ta có thể thêm bước xác thực trước khi đổi mật khẩu.
--------------------------------------------------
*/
void User::setPassword(const string& newPassword) {
    password = newPassword;
    cout << "Password updated.\n";
}

void User::setName(const string& newName) {
    name = newName;
    cout << "Name updated.\n";
}

/*
--------------------------------------------------
verifyPassword(): hàm nội bộ dùng để so sánh mật khẩu thật.
=> Không public ra ngoài — giúp đảm bảo **Encapsulation**.
--------------------------------------------------
*/
bool User::verifyPassword(const string& inputPassword) const {
    return password == inputPassword;
}



```

---


#### Student.h
```cpp
#pragma once
#include "User.h"
#include "Assignment.h"
#include "Submission.h"
#include <vector>
#include <memory>

// Lớp Student kế thừa từ User và enable_shared_from_this<Student>
// -> giúp đối tượng Student có thể tự tạo shared_ptr trỏ đến chính nó.
// em khai báo lớp Student kế thừa từ User và enable_shared_from_this<Student>.
enable_shared_from_this cho phép lớp này tự tạo ra con trỏ thông minh shared_ptr trỏ tới chính nó, giúp tránh lỗi bộ nhớ khi cần truy xuất chính đối tượng hiện tại

class Student : public User, public enable_shared_from_this<Student> {
private:
    string studentID;  // Mã sinh viên
    vector<shared_ptr<Submission>> submissions;  // Danh sách bài nộp

public:
    // Constructor khởi tạo Student với username, password, name và studentID
    Student(const string& username, const string& password,
            const string& name, const string& studentID);

    // Destructor ảo để bảo đảm hủy đúng cách khi dùng đa hình
    ~Student() override = default;

    // Định nghĩa lại hàm ảo thuần túy từ User
    string getUserType() const override;  
    void displayMenu() override;          

    // Sinh viên nộp bài
    shared_ptr<Submission> submitAssignment(shared_ptr<Assignment> assignment, const string& content);

    // Lấy mã sinh viên
    string getStudentID() const;
};


```

#### Student.cpp
```cpp
#include "Student.h"
#include <ctime>
#include <sstream>
#include <iomanip>

// Constructor: gọi lại constructor của User để khởi tạo thông tin chung
Student::Student(const string& username, const string& password,
                 const string& name, const string& studentID)
    : User(username, password, name), studentID(studentID) {}

// Hàm này ghi đè (override) hàm ảo thuần túy trong lớp User — thể hiện tính đa hình (Polymorphism).
Khi gọi user->getUserType() (trỏ đến Student), chương trình biết đây là “Student”.

string Student::getUserType() const { return "Student"; }


// Hàm hiển thị menu dành riêng cho sinh viên.
void Student::displayMenu() {
    cout << "\n=== Student Menu ===\n";
    cout << "1. Submit Assignment\n";
    cout << "2. View Submissions\n";
}
// Ở dòng đầu tiên, shared_ptr<Submission> là kiểu trả về — em dùng shared_ptr để quản lý tự động vùng nhớ cho bài nộp.
// Tiếp theo là phần khai báo Student::submitAssignment, cho thấy đây là hàm thành viên của lớp Student.
// if (!assignment) nghĩa là nếu assignment rỗng hoặc không hợp lệ.

shared_ptr<Submission> Student::submitAssignment(shared_ptr<Assignment> assignment, const string& content) {
    if (!assignment) {
        cout << "Invalid assignment.\n";
        return nullptr;
    }

    // Lấy thời gian hiện tại
    // time(nullptr) trả về thời điểm hiện tại dưới dạng time_t.
    // localtime_s(&tm, &now) chuyển time_t → struct tm theo timezone cục bộ. Lưu ý: localtime_s là phiên bản an toàn cho Windows; trên POSIX có localtime_r.
    // ostringstream + put_time định dạng thời gian thành chuỗi "YYYY-MM-DD HH:MM:SS". Kết quả nằm trong oss.str().
    auto now = time(nullptr);
    struct tm tm;
    localtime_s(&tm, &now);
    ostringstream oss;
    oss << put_time(&tm, "%Y-%m-%d %H:%M:%S");

    // Kiểm tra xem bài nộp có trễ hạn không
    // isSubmissionLate(...) so sánh thời gian nộp với deadline của assignment và trả về true nếu trễ.
    bool late = assignment->isSubmissionLate(oss.str());
    // 
    string submissionID = "SUB_" + studentID + "_" + to_string(now);

    // Tạo đối tượng Submission (dùng shared_ptr)
    // make_shared<Submission>(...) cấp phát và trả về shared_ptr<Submission>.
    // shared_from_this() trả về shared_ptr<Student> trỏ đến this
    auto submission = make_shared<Submission>(submissionID, shared_from_this(), content, oss.str(), late);

    // Lưu vào danh sách submissions của sinh viên
    submissions.push_back(submission);

    // Đồng thời thêm vào Assignment tương ứng
    assignment->addSubmission(submission);

    cout << "Submitted successfully (" << (late ? "Late" : "On Time") << ").\n";
    return submission;
}
// Getter đơn giản, trả về mã sinh viên.
string Student::getStudentID() const { return studentID; }

```

---

#### Submission.h
```cpp
#pragma once
#include <string>
#include <memory>
#include <iostream>
#include <sstream>
#include <iomanip>
using namespace std;

class Student;
// private, em khai báo các thuộc tính dữ liệu chính của bài nộp:
// submissionID là mã định danh của bài nộp, giúp phân biệt giữa các bài khác nhau.
// shared_ptr<Student> student là con trỏ thông minh trỏ tới đối tượng sinh viên đã nộp bài.
// content là nội dung bài làm mà sinh viên nộp.
// submissionDate lưu ngày giờ nộp bài.
// isLate là cờ logic cho biết sinh viên nộp bài trễ hay đúng hạn.
// Cuối cùng là grade, lưu điểm số của bài nộp, có thể được cập nhật sau khi chấm.

class Submission {
private:
    string submissionID;
    shared_ptr<Student> student;
    string content;
    string submissionDate;
    bool isLate;
    double grade;

public:
// Constructor này giúp khởi tạo đầy đủ thông tin cho một bài nộp mới — bao gồm ID, sinh viên nộp, nội dung, thời gian, và trạng thái trễ hạn.
    Submission(const string& submissionID, shared_ptr<Student> student,
               const string& content, const string& submissionDate, bool isLate);

    // Getters getter – các hàm lấy thông tin ra bên ngoài mà vẫn đảm bảo tính đóng gói
    // getSubmissionID() trả về mã bài nộp,
    string getSubmissionID() const;
    // getStudent() trả về con trỏ sinh viên,
    shared_ptr<Student> getStudent() const;
    // getSubmissionDate() và getContent() để lấy ngày nộp và nội dung,
    string getSubmissionDate() const;
    string getContent() const;
    // getIsLate() cho biết bài nộp trễ hay không
    bool getIsLate() const;
    // getGrade() để xem điểm số hiện tại.
    double getGrade() const;

    // Setters
    // setGrade(double newGrade) để giáo viên chấm và cập nhật điểm cho bài nộp.
    void setGrade(double newGrade);

    // Display
    void displaySubmission() const;
};

```

#### Submission.cpp
```cpp
#include "Submission.h"
#include "Student.h"

// Tiếp theo là hàm khởi tạo (constructor) của lớp Submission.
// submissionID, student, content, submissionDate, và isLate được gán trực tiếp từ các tham số truyền vào.
// Riêng grade được gán giá trị mặc định là -1, thể hiện rằng bài nộp này chưa được chấm điểm
Submission::Submission(const string& submissionID, shared_ptr<Student> student,
                       const string& content, const string& submissionDate, bool isLate)
    : submissionID(submissionID), student(student), content(content),
      submissionDate(submissionDate), isLate(isLate), grade(-1) {}

// getter — những hàm truy cập dữ liệu, cho phép lấy thông tin của bài nộp nhưng không cho phép sửa trực tiếp
// từ khóa const ở cuối,nghĩa là chúng không thay đổi trạng thái của đối tượng, đảm bảo tính an toàn và tuân thủ nguyên tắc Encapsulation trong lập trình hướng đối tượng

string Submission::getSubmissionID() const { return submissionID; }
shared_ptr<Student> Submission::getStudent() const { return student; }
string Submission::getSubmissionDate() const { return submissionDate; }
string Submission::getContent() const { return content; }
bool Submission::getIsLate() const { return isLate; }
double Submission::getGrade() const { return grade; }

// setGrade, cho phép giáo viên hoặc hệ thống chấm điểm cập nhật lại điểm số cho bài nộp.
void Submission::setGrade(double newGrade) { grade = newGrade; }


// displaySubmission(), dùng để hiển thị toàn bộ thông tin của bài nộp
void Submission::displaySubmission() const {
    cout << "\n--- Submission Info ---\n";
    cout << "Submission ID: " << submissionID << endl;
    cout << "Student: " << (student ? student->getName() : "Unknown") << endl;
    cout << "Submitted at: " << submissionDate << endl;
    cout << "Late: " << (isLate ? "Yes" : "No") << endl;
    cout << "Content: " << content << endl;
    if (grade >= 0) cout << "Grade: " << grade << endl;
    cout << "-----------------------\n";
}

```

---


#### Assignment.h
```cpp
#pragma once
#include <string>
#include <vector>
#include <memory>
#include <ctime>
#include <iostream>
using namespace std;
// là forward declaration, tức là em chỉ khai báo tên lớp Submission để lớp Assignment có thể sử dụng con trỏ trỏ đến Submission mà không cần include toàn bộ file Submission.h
class Submission;

class Assignment {
private:
    string assignmentID;
    string title;
    string description;
    string deadline;
    vector<shared_ptr<Submission>> submissions;
    bool isActive;

public:
// constructor để khởi tạo bài tập với ID
    Assignment(const string& assignmentID, const string& title,
               const string& description, const string& deadline);

    // addSubmission(...): dùng để thêm một bài nộp mới vào danh sách
    void addSubmission(shared_ptr<Submission> submission);
    // isSubmissionLate(...): kiểm tra xem bài nộp có trễ hạn không
    bool isSubmissionLate(const string& submissionDate) const;
    // isDeadlinePassed(): kiểm tra xem hạn nộp đã hết chưa,
    bool isDeadlinePassed() const;

// để hiển thị thông tin bài tập và danh sách các bài nộp.
    void displayAssignment() const;
    void displaySubmissions() const;
// để lấy thông tin cơ bản
    string getAssignmentID() const;
    string getDeadline() const;
// để lấy ngày hiện tại – thường dùng khi kiểm tra hạn nộp.
    string getCurrentDate() const;
};

```

#### Assignment.cpp
```cpp
#include "Assignment.h"
#include "Submission.h"
#include <sstream>
#include <iomanip>

// constructor của lớp Assignment
// nó gán giá trị cho các thuộc tính assignmentID, title, description, deadline,
và mặc định isActive là true, nghĩa là bài tập đang còn hiệu lực.

Assignment::Assignment(const string& assignmentID, const string& title,
                       const string& description, const string& deadline)
    : assignmentID(assignmentID), title(title), description(description),
      deadline(deadline), isActive(true) {}
// Sang hàm addSubmission:
// Hàm này nhận vào một shared_ptr<Submission> và thêm nó vào vector submissions
// Em dùng shared_ptr để đảm bảo bộ nhớ được quản lý tự động và bài nộp không bị xoá khi còn được tham chiếu bởi các đối tượng khác.
void Assignment::addSubmission(shared_ptr<Submission> submission) {
    submissions.push_back(submission);
    cout << "Submission added to assignment.\n";
}


// Ở đây em định nghĩa hàm này có nhiệm vụ kiểm tra xem bài nộp có bị trễ hạn hay không,

bool Assignment::isSubmissionLate(const string& submissionDate) const {
// bằng cách so sánh giữa thời gian hạn nộp (deadline) và thời gian sinh viên thực tế nộp (submissionDate)
// Em khai báo hai cấu trúc tm để lưu thông tin thời gian —một cái dùng cho deadline và một cái cho submission date.
    struct tm tm_deadline = {}, tm_submit = {};
    // Dấu {} ở đây giúp khởi tạo tất cả các giá trị ban đầu về 0
    // Em tạo hai luồng chuỗi istringstream để đọc dữ liệu từ các chuỗi thời gian.
    istringstream ss1(deadline), ss2(submissionDate);
    // Vì deadline và submissionDate đều đang ở dạng chuỗi "YYYY-MM-DD HH:MM:SS
    ss1 >> get_time(&tm_deadline, "%Y-%m-%d %H:%M:%S");
    ss2 >> get_time(&tm_submit, "%Y-%m-%d %H:%M:%S");
    // get_time() giúp đọc chuỗi thời gian theo đúng định dạng năm–tháng–ngày–giờ–phút–giây
    time_t t1 = mktime(&tm_deadline), t2 = mktime(&tm_submit);
    // em chuyển hai cấu trúc tm sang kiểu time_t
    return difftime(t2, t1) > 0;
}
// Hàm isDeadlinePassed chỉ đơn giản là gọi getCurrentDate() để lấy thời gian hiện tại và kiểm tra xem deadline đã qua chưa:
bool Assignment::isDeadlinePassed() const {
    string now = getCurrentDate();
    return isSubmissionLate(now);
}

// dùng để in thông tin chi tiết của bài tập ra màn hình:
void Assignment::displayAssignment() const {
    cout << "\n=== Assignment ===\n";
    cout << "ID: " << assignmentID << endl;
    cout << "Title: " << title << endl;
    cout << "Deadline: " << deadline << endl;
    cout << "Active: " << (isActive ? "Yes" : "No") << endl;
    cout << "Submissions: " << submissions.size() << endl;
}
// Hàm displaySubmissions() thì duyệt qua toàn bộ danh sách bài nộp:
nếu chưa có bài nào thì in "No submissions yet."
// thì gọi displaySubmission() của từng Submission để hiển thị thông tin chi tiết.
void Assignment::displaySubmissions() const {
    if (submissions.empty()) cout << "No submissions yet.\n";
    for (auto& s : submissions) s->displaySubmission();
}

// giúp lấy thông tin cơ bản.
string Assignment::getAssignmentID() const { return assignmentID; }
string Assignment::getDeadline() const { return deadline; }

// Hàm này dùng time() để lấy thời gian hiện tại, rồi định dạng lại thành chuỗi "YYYY-MM-DD HH:MM:SS".
Đây là công cụ giúp chương trình xác định bài nộp có đúng hạn không.

string Assignment::getCurrentDate() const {
    auto now = time(nullptr);
    struct tm tm;
    localtime_s(&tm, &now);
    ostringstream oss;
    oss << put_time(&tm, "%Y-%m-%d %H:%M:%S");
    return oss.str();
}

```

---

### Main
```cpp
#include "Student.h"
#include "Assignment.h"
#include <memory>

int main() {
// Ở đây, make_shared sẽ cấp phát bộ nhớ và trả về một shared_ptr<Student> để quản lý sinh viên an toàn.
// Tham số truyền vào lần lượt là tên đăng nhập, mật khẩu, tên sinh viên, và mã sinh viên
    auto student = make_shared<Student>("sv01", "123", "Nguyen Van A", "ST001");
    // Tham số truyền vào lần lượt là tên đăng nhập, mật khẩu, tên sinh viên, và mã sinh viên.
    student->login("123");
// Nếu mật khẩu đúng, chương trình sẽ báo đăng nhập thành công — bước này giúp thể hiện chức năng xác thực người dùng.

// em tạo một đối tượng bài tập (Assignment):
// Bài tập này có ID là "A01", tiêu đề "Lab 1", mô tả "OOP Practice", và hạn nộp là ngày 5 tháng 11 năm 2025 lúc 23:59:59.
    auto assignment = make_shared<Assignment>("A01", "Lab 1", "OOP Practice", "2025-11-05 23:59:59");

// Khi sinh viên muốn nộp bài, em gọi:
    student->submitAssignment(assignment, "This is my lab solution.");
    // Sau khi nộp xong, em hiển thị thông tin bài tập bằng:
    assignment->displayAssignment();
    // Hàm này sẽ in ra ID, tiêu đề, deadline, trạng thái hoạt động, và số lượng bài nộp hiện tại.
    // để liệt kê chi tiết các bài nộp, bao gồm tên sinh viên, thời gian nộp, nội dung, và trạng thái nộp trễ hay đúng hạn
    assignment->displaySubmissions();

    return 0;
}

```
