# Student Management System

## Project Description
The Student Management System is a lightweight, web-based application designed to manage student records, courses, and attendance efficiently. Built with Python (Flask) and SQLite, it provides a simple yet powerful interface for educational institutions or individuals to perform CRUD operations, manage course enrollments, and track attendance.

---

## Project Details

### Features
- Add, view, update, and delete student records
- Manage courses and assign students to courses
- Mark and visualize student attendance
- Search and filter student data
- Responsive web interface for ease of use

### Functional Overview
- **Student Management:** Register new students, update their details, and view all student records.
- **Course Management:** Add new courses, assign students to courses, and view all available courses.
- **Attendance Tracking:** Mark attendance for students in specific courses and visualize attendance data.
- **Database Integration:** Uses SQLite for persistent, file-based storage.

---

## Tech Stack
- **Backend:** Python, Flask
- **Database:** SQLite (`college.db`)
- **Frontend:** HTML, CSS (custom), JavaScript

---

## Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/DCode-v05/Student-Management-System.git
cd Student-Management-System
```

### 2. Install Dependencies
```bash
pip install flask matplotlib
```

### 3. Run the Application
```bash
python app.py
```
Visit [http://127.0.0.1:5000/](http://127.0.0.1:5000/) in your browser to start using the system.

---

## Usage
- **Add Student:** Navigate to the 'Add Student' page to register a new student.
- **View Students:** View, search, and update student records.
- **Add Course:** Add new courses and assign students.
- **View Courses:** List all available courses.
- **Mark Attendance:** Record attendance for students in specific courses.
- **Attendance Data:** Visualize attendance records for each student.

---

## Project Structure
```bash
Student-Management-System/
│
├── app.py              # Main Flask application
├── college.db          # SQLite database
├── README.md           # Project documentation
│
├── templates/          # HTML templates
│   ├── add_course.html
│   ├── add_student.html
│   ├── attendance_data.html
│   ├── index.html
│   ├── mark_attendance.html
│   ├── update_student.html
│   ├── view_courses.html
│   └── view_students.html
│
└── static/             # Static files (CSS)
    └── styles.css
```

---

## Contributing

Contributions are welcome! To contribute:
1. Fork the repository
2. Create a new branch:
   ```bash
   git checkout -b feature/your-feature
   ```
3. Commit your changes:
   ```bash
   git commit -m "Add your feature"
   ```
4. Push to your branch:
   ```bash
   git push origin feature/your-feature
   ```
5. Open a pull request describing your changes.

---

## Contact
- **GitHub:** [DCode-v05](https://github.com/DCode-v05)
- **Email:** denistanb05@gmail.com
