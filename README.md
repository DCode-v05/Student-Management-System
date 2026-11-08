# Student Management System

A Flask app for keeping student records, courses and attendance in SQLite, with a per-student attendance chart. It is a single file, `app.py`, with server-rendered Jinja templates.

## How it works

Each route opens its own `sqlite3` connection to `college.db`, runs parameterised SQL and either redirects or renders a template. `init_db()` creates the tables on startup if they are missing:

- `students`: text `student_id` plus personal and academic fields (department, semester, credits, date of birth, email, phone, address, enrollment year, course name)
- `courses`: auto-increment `course_id`, name, department, semester
- `attendance`: student, course, date, status (Present or Absent), with foreign keys to both
- `student_courses`: join table between students and courses

Routes:

| Route | What it does |
|---|---|
| `/` | Landing page and navigation |
| `/add_student` | Form to add a student |
| `/generate_student_id` | JSON endpoint that suggests an ID while you fill in the form |
| `/view_students` | List students; the search box filters by ID or name |
| `/update_student` | Rewrite a student's record by ID |
| `/add_course` | Add a course and link it to a student |
| `/view_courses` | List courses |
| `/mark_attendance` | Record Present or Absent for a student, course and date |
| `/attendance_data/<student_id>` | Matplotlib line chart of that student's attendance, embedded as base64 |

**Student IDs.** `generate_student_id()` takes the enrollment year suffix, a two-letter department code and a three-digit number. The number is the new name's alphabetical position among the students already in that department, so `23CS001` is the first name in CS for 2023. Adding a name that sorts earlier can produce an ID that already exists.

**Chart.** Present is plotted as 1 and Absent as 0 over the recorded dates.

## Stack

Python, Flask, Jinja2, sqlite3, Matplotlib, one CSS file and a small `fetch` call for the ID suggestion.

## Setup

There is no `requirements.txt`. The code imports Flask and Matplotlib:

```bash
pip install flask matplotlib
python app.py
```

Flask runs in debug mode on http://localhost:5000. `college.db` is included and holds a couple of sample records.

## Structure

```
app.py         routes, schema, ID generator, chart
college.db     SQLite database with sample rows
templates/     add/view/update student, courses, attendance pages
static/        styles.css
```
