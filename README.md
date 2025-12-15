Software Requirements Specification (SRS) for “Quiz Master” Web Application
1. Introduction
1.1 Purpose
The purpose of this document is to define the functional and non-functional requirements for the “Quiz Master” web application — a quiz-management and quiz-taking platform. This document serves as a blueprint for developers, testers, and stakeholders to ensure consistent understanding of the system to be built using Flask, Jinja2, and SQLite.
1.2 Scope
This document covers:
All user roles (Administrator, Teacher /Faculty / Question setter and Student / User) and their interactions.
Data storage and structure (SQLite).
Front-end templates and page flows (using Jinja2).
Business logic for quiz creation, management, user registration/login, quiz taking, and result tracking.
1.3 Definitions / Acronyms
Admin — user with privileges to view and manage teacher, student and their activities.
Teacher / Faculty / Question setter – user with privileges to create subjects/ chapters /quizzes /questions and manage content.
User — a registered quiz-taker.
Quiz — a set of questions belonging to a specific chapter/subject, scheduled at some date & duration.
Chapter — subdivision under a Subject.
Subject — highest-level category for quizzes (e.g., Physics, Math).
CRUD — Create, Read, Update, Delete.
SRS — Software Requirements Specification.


2. Overall Description
2.1 User Classes and Stakeholders
Role / Stakeholder
Description / Responsibilities
Teacher 
Create and manage Subjects, Chapters, Quizzes, Questions. Also manage user registrations if needed; view summary charts and manage content.
Registered User
Register, login, view list of upcoming quizzes, take quizzes, see scores, view summary charts.
System (Backend)
Serve web pages, handle business logic (creation of content, quiz scheduling, user authentication / authorization, quiz-taking logic), persist data to SQLite.
Database (SQLite)
Store users, subjects, chapters, quizzes, questions, quiz attempts, scores, metadata (timestamps, etc.).

2.2 Assumptions and Dependencies
The system will run on a server (local dev or production) where Python and SQLite are available.
Dependencies: Flask (for routing, request handling), Jinja2 (templating), standard Python library’s sqlite3 module. 
Templates (HTML) will render pages based on data passed from Flask via Jinja2 variables.
Basic front-end styling may be done with CSS/Bootstrap (optional), but UI behavior (navigation flows) is defined by the wireframe.
No complex distributed data or external dependencies (APIs, external services) initially.

3. Functional Requirements
Below are the key functional requirements. Each is numbered for traceability.
3.1 User Registration & Authentication
FR-1: The system shall allow a new user to register by providing: username (email), password, full name, qualification, date of birth. (As per wireframe registration form)
FR-2: The system shall allow existing users to log in with username and password via a login page. (As per wireframe login form)
FR-3: After login, the user shall be redirected to their respective dashboard (Admin or User) based on role.
FR-4: The system shall enforce that only authenticated users can access user-specific pages (dashboard, quizzes, results).
FR-5 (optional but recommended): Passwords must be securely stored (e.g. hashed) — though with SQLite, at least avoid plain-text storage.
3.2 Role-Based Access / Navigation
FR-6: The system shall support three roles: Admin, Teacher and User.
FR-7: Teacher - specific pages: manage Subjects, Chapters, Quizzes, Questions; view summary charts of quiz attempts/scores.
FR-8: User-specific pages: view upcoming quizzes, take quizzes, view past quiz scores, view summary charts.
3.3 Content Management (Teacher)
FR-9: Teacher can create a Subject (with Name, Description). Wireframe shows “New Subject” form.
FR-10: Teacher can view a list of all existing Subjects. Wireframe shows an “Teacher   Dashboard — Subjects” section.
FR-11: Teacher can create a Chapter under a specific Subject (with Name, Description). Wireframe shows “New Chapter” form.
FR-12: Teacher can view Chapters under a given Subject.
FR-13: Teacher can create a Quiz tied to a particular Chapter, specifying: Chapter ID, Date (schedule), Duration (time). (Wireframe: “New Quiz” form)
FR-14: Admin can view a list of all Quizzes (with info: ID, number of questions, date/time, duration) grouped by Chapter/Subject. (Wireframe: “Quiz Management” section)
FR-15: Teacher can delete or edit a Quiz. (CRUD for quizzes)
FR-16: Teacher can create Questions tied to a particular Chapter (or Quiz), with fields: question title, question statement, options (e.g. 4), and correct option. (Wireframe: “New Question” form)
FR-17: Teacher can view list of questions for a quiz, edit or delete them.
3.4 Quiz Taking (User)
FR-18: User dashboard should list upcoming available quizzes (Quiz ID, number of questions, date, time) with actions to “View” details or “Start” quiz. (Wireframe: “Upcoming Quizzes”)
FR-19: When user starts a quiz, the system shall display questions one by one (or all at once — according to design), with radio-button options and controls like “Save and Next” or “Submit”. (Wireframe: “Start the Quiz” page)
FR-20: System should enforce quiz duration (timer) — prevent submission after time expires. (Wireframe shows a timer)
FR-21: On submission or completion, system should evaluate answers and calculate score.
FR-22: System should store the user’s quiz attempt: user ID, quiz ID, timestamp, score, responses.
3.5 Results & History
FR-23: User should be able to view past quiz attempts with details: Quiz ID, date, score, number of questions. (Wireframe: “Quiz Scores”)
FR-24: User should be able to view summary charts — e.g. a bar chart of attempts by subject or number of quizzes attempted over time. (Wireframe: “Summary Charts”)
FR-25: Admin should have access to charts/summary of overall usage: e.g. subject-wise number of quizzes, top scores, user attempts. (Wireframe: Admin “Summary Charts”)
3.6 Navigation & UI Behavior
FR-26: Navigation bar / menu should allow access to home, quizzes, summary, logout (for the all three roles; items shown based on role). (As per top menu in wireframe)
FR-27: For Teacher: from Dashboard → ability to navigate to Subject list → to Chapters → to Quizzes → to Questions.
FR-28: For User: from Dashboard → Upcoming Quizzes → Start Quiz → Quiz page → Submission → Redirect to Scores/History.
5. Data Model / Database Requirements
Here’s a high-level conceptual database schema (tables and key columns) to support requirements. You can change the tables as per your enhancement in features.
Users: user_id (PK), username (email, unique), password_hash, full_name, qualification, date_of_birth, role (enum: user/admin), registration_date, other metadata.
Subjects: subject_id (PK), name, description, created_at, updated_at.
Chapters: chapter_id (PK), subject_id (FK → Subjects), name, description, created_at, updated_at.
Quizzes: quiz_id (PK), chapter_id (FK → Chapters), scheduled_date (datetime), duration (minutes), created_at, updated_at, status (active/inactive).
Questions: question_id (PK), quiz_id (FK → Quizzes) or chapter_id (depending on design), question_title, question_statement (text), option_1, option_2, option_3, option_4 (or more), correct_option (integer or enum), created_at, updated_at.
Quiz_Attempts: attempt_id (PK), user_id (FK → Users), quiz_id (FK → Quizzes), start_time, end_time, score, total_questions, submitted (boolean), created_at.
User_Answers: answer_id (PK), attempt_id (FK → Quiz_Attempts), question_id (FK → Questions), selected_option, is_correct (boolean), created_at.
Constraints: foreign keys, data types, uniqueness (e.g. unique username), referential integrity.

6. Use Cases / User Stories
Here are representative user stories / use cases (for both Admin and User roles):
UC-1 (User Registration): As a new user, I want to register an account by entering my email, password, and personal details, so that I can log in and take quizzes.
UC-2 (User Login): As a registered user (or admin), I want to log in using my credentials, so that I access my dashboard.
UC-3 (Teacher – Create Subject/Chapter/Quiz/Question): As an admin, I want to create a subject, then chapters under it, then quizzes under chapters, and add questions, so that I can build quiz content for users.
UC-4 (Teacher – Manage Content): As an admin, I want to view and modify or delete existing subjects/chapters/quizzes/questions, so I can keep the content up-to-date.
UC-5 (User – View Available Quizzes): As a user, after login I want to see a list of upcoming quizzes I can attempt.
UC-6 (User – Take Quiz): As a user, I want to start a quiz, answer questions, and submit, and receive my score.
UC-7 (User – View Past Results): As a user, I want to view my past quiz attempts and scores.
UC-8 (User/Teacher/Admin – View Summary / Charts): As a user or admin, I want to see summary statistics such as number of attempts per subject, past performance, etc.

7. UI / Navigation Specification
Based on the wireframe, provide a mapping of pages / routes in Flask + Jinja2:
Page / Route
Description / Purpose
/register
Show registration form (new user).
/login
Show login form (existing user).
/logout
Log out user, clear session.
/dashboard
After login, show dashboard (Admin or User based on role).
/ teacher /subjects
List all subjects (Admin view). Option to add new subject.
/ teacher/subjects/new
Form to create a new subject.
/teacher /subjects/<subject_id>/chapters
List chapters under the subject; option to add new chapter.
/teacher/chapters/<chapter_id>/quizzes
List existing quizzes in the chapter; option to add new quiz.
/ teacher/quizzes/new
Form to create a new quiz (select chapter, schedule date, duration).
/ teacher/quizzes/<quiz_id>/questions
List of questions under the quiz; option to add new question.
/teacher/quizzes/<quiz_id>/
questions/new
Form to create a new question (title, statement, options, correct answer).
/quizzes
For user: list all upcoming quizzes available to attempt.
/quizzes/<quiz_id>/start
Start quiz — show quiz questions page (with timer) as per wireframe.
/quizzes/<quiz_id>/submit
Submit quiz answers; calculate results; redirect to result page / history.
/results
Show user’s past quiz attempts (history, scores).
/summary
Show summary charts (user or admin depending on role).

All HTML templates will be written using Jinja2, with data passed from Flask route handlers. 

8. References / Standards
Use of SRS / Software Requirement Specification template recommended for clarity and traceability. 
Use of functional specification to outline “what” the system should do — separate from implementation details. 
Use of Flask + SQLite + Jinja2 as a proven lightweight stack for building CRUD-based web applications

Wireframe: 

https://www.figma.com/proto/ubisvNl4mupJHwxQnCoEvc/wire-frame-for-Quiz-master?page-id=0%3A1&node-id=6-142&viewport=324%2C184%2C0.09&t=2Hpr16PPjXGLyUvT-1&scaling=min-zoom&content-scaling=fixed&starting-point-node-id=3%3A2&show-proto-sidebar=1
