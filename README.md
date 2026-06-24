# Student Management System

**A small Flask web app for keeping college student records, courses, and attendance in one place — backed by SQLite, with attendance plotted as a chart.**

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white) ![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white) ![SQLite](https://img.shields.io/badge/SQLite-003B57?style=flat&logo=sqlite&logoColor=white) ![Jinja](https://img.shields.io/badge/Jinja-B41717?style=flat&logo=jinja&logoColor=white) ![Matplotlib](https://img.shields.io/badge/Matplotlib-11557C?style=flat&logo=python&logoColor=white) ![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white) ![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=flat&logo=css3&logoColor=white)

## Overview

This is a web-based admin tool for managing the data a small department deals with day to day: who the students are, what courses exist, and whether students showed up. It's a single Flask file (`app.py`) talking to a file-based SQLite database (`college.db`), with Jinja2 templates for the pages and a single CSS file for styling.

The idea was to build a working records system end to end — schema, routes, forms, and a bit of data visualization — rather than a toy single-page form. It covers registering students, listing and searching them, editing existing records, adding courses, marking attendance per student per course, and drawing an attendance timeline as a Matplotlib chart rendered straight into the page. It's a college learning project and it's complete for what it sets out to do.

## Key Features

- **Student registration** — a form captures student ID, name, department, semester, credits, date of birth, email, phone, address, enrollment year, and the course name, then writes the record to the `students` table.
- **Auto-suggested student ID** — as you type the name, department, and enrollment year on the Add Student page, an AJAX call hits a `/generate_student_id` endpoint that builds an ID like `25CS003` from the year suffix, a two-letter department code, and the student's alphabetical position within their department.
- **View and search students** — lists every student, and a search box filters by student ID or name using a `LIKE` query against the database.
- **Edit student records** — an update route rewrites an existing student's full row by matching on student ID.
- **Course management** — add a course (name, department, semester) and link it to a student through a join table, then view the full course list.
- **Attendance marking** — pick a student and a course from dropdowns, choose a date, and record Present or Absent. The status is stored against the course's internal ID.
- **Attendance visualization** — for a given student, the app pulls their attendance rows, maps Present/Absent to 1/0, and renders a Matplotlib line chart of status over time. The chart is base64-encoded and embedded inline so no image file is written to disk.
- **Relational schema with foreign keys** — four tables (`students`, `courses`, `attendance`, `student_courses`) with foreign-key references tying attendance and enrollment back to students and courses.
- **Server-rendered UI** — every page is a Jinja2 template sharing one stylesheet, with a simple nav linking the main sections.

## How It Works

### Application layer

Everything lives in `app.py` as a flat set of Flask routes. There's no blueprint split or ORM — each route opens its own `sqlite3` connection, runs parameterized SQL, commits, closes, and either redirects or renders a template. Forms post back to the same route that served them (`GET` shows the form, `POST` handles the submit), which keeps the request flow easy to follow.

The route map:

- `/` — landing page with the navigation menu.
- `/add_student` — GET shows the form, POST inserts a new student.
- `/generate_student_id` — JSON endpoint that returns a suggested ID.
- `/view_students` — lists all students; POST runs the search filter.
- `/update_student` — POST updates an existing student by ID.
- `/add_course` — inserts a course and an entry in the student-course join table.
- `/view_courses` — lists all courses.
- `/mark_attendance` — GET loads student and course dropdowns, POST records attendance.
- `/attendance_data/<student_id>` — builds and returns the attendance chart.

### Database

On startup, `init_db()` creates the tables if they don't already exist:

- `students` — keyed by a text `student_id`, with the full set of personal and academic fields.
- `courses` — auto-incrementing `course_id`, plus name, department, and semester.
- `attendance` — one row per mark, with `student_id`, `course_id`, `date`, and `status`, and foreign keys back to both parent tables.
- `student_courses` — a join table linking students to courses.

Queries are written with `?` placeholders rather than string formatting, so the SQL is parameterized.

### The student-ID generator

`generate_student_id()` is the one piece of non-trivial logic. Given a name, department, and enrollment year, it pulls every existing student in that department, inserts the new name into the sorted list, finds its alphabetical position, and builds the ID as: last two digits of the year, first two letters of the department uppercased, then the position zero-padded to three digits. The front end calls this through `fetch()` on every keystroke in the relevant fields and drops the result into a read-only field as a suggestion. Note it's a helper — the actual save form has its own required `student_id` input that the user fills in, so the generated value is a hint rather than something wired directly into the insert.

### Attendance chart

The `/attendance_data/<student_id>` route reads the student's attendance records, converts each status to 1 (Present) or 0 (Absent), and uses Matplotlib to draw a line chart of attendance over the recorded dates. Instead of saving a PNG, it writes the figure to an in-memory `BytesIO` buffer, base64-encodes it, and hands that string to a template so the chart shows up inline in the browser.

## Tech Stack

- **Language:** Python (Flask app), plus HTML, CSS, and a little inline JavaScript.
- **Framework:** Flask, with Jinja2 templating.
- **Database:** SQLite (`college.db`), accessed through the standard-library `sqlite3` module.
- **Visualization:** Matplotlib (server-side chart rendered to base64).
- **Frontend:** plain HTML forms, one custom `styles.css`, and a vanilla-JS `fetch` call for the ID suggestion.

## Getting Started

### Prerequisites
- Python 3
- Flask and Matplotlib

### Installation
```bash
git clone https://github.com/DCode-v05/Student-Management-System.git
cd Student-Management-System
pip install flask matplotlib
```

### Running
```bash
python app.py
```

On start, `init_db()` creates the SQLite tables if they don't exist, then the dev server comes up. Open the app at:

```
http://127.0.0.1:5000/
```

## Usage

From the home page, use the nav to move between sections:

- **Add Student** — fill in the form and submit; the record is saved and you're redirected to the student list. As you type, the ID field suggests a generated student ID.
- **View Students** — see every student, or type in the search box to filter by ID or name.
- **Add Course** — add a course and tie it to a student.
- **View Courses** — list all courses.
- **Mark Attendance** — choose a student and course, pick a date, set Present or Absent, and submit.
- **Attendance chart** — hitting `/attendance_data/<student_id>` renders that student's attendance as a line chart over time.

## Project Structure

```
Student-Management-System/
├── app.py                          # Flask app: all routes, DB setup, ID generator, chart
├── college.db                      # SQLite database (students, courses, attendance, joins)
├── static/
│   └── styles.css                  # Shared stylesheet for all pages
├── templates/
│   ├── index.html                  # Home page + navigation
│   ├── add_student.html            # Add-student form + ID-suggestion JS
│   ├── view_students.html          # Student list + search
│   ├── update_student.html         # Edit-student form
│   ├── add_course.html             # Add-course form
│   ├── view_courses.html           # Course list
│   ├── mark_attendance.html        # Attendance form (student/course dropdowns)
│   └── attendance_data.html        # Attendance view
└── README.md
```

---

## Contact

<table>
  <tr><td><b>Portfolio:</b> <a href="https://www.denistan.me">Denistan</a></td><td><b>LinkedIn:</b> <a href="https://www.linkedin.com/in/denistanb">denistanb</a></td></tr>
  <tr><td><b>GitHub:</b> <a href="https://github.com/DCode-v05">DCode-v05</a></td><td><b>LeetCode:</b> <a href="https://leetcode.com/u/Denistan_B">Denistan_B</a></td></tr>
  <tr><td colspan="2" align="center"><b>Email:</b> <a href="mailto:denistanb05@gmail.com">denistanb05@gmail.com</a></td></tr>
</table>

Made with ❤️ by **Denistan B**
