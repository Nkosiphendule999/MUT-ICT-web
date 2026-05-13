# MUT ICT Department Website

## Overview

A full-stack web application for the Department of Information & Communication Technology at Mangosuthu University of Technology (MUT). The system features user authentication, role-based access control, event management, consultation booking, peer support program, AI chatbot, admin dashboard, and report export functionality.

## Features

### User Features
- **User Authentication** - Login/Register system with role-based access (Admin/Student/External)
- **Events Management** - View upcoming ICT events with search, filter, and pagination
- **Consultation Booking** - Book appointments with ICT lecturers
- **Peer Support Program** - Sign up for peer mentoring and tutoring
- **AI Chatbot Assistant** - 24/7 virtual assistant for FAQs and course recommendations
- **User Profile** - Update personal information and change password
- **View Booking History** - See all consultation bookings and registered events

### Admin Features
- **Admin Dashboard** - View statistics and analytics
- **Event Management** - Create, edit, and delete events (full CRUD)
- **Consultation Management** - View and manage all consultation bookings
- **Export Reports** - Download Events, Consultations, and Users reports (Excel/PDF)
- **Access Control** - Middleware-protected admin routes

### Technical Features
- **Database Integration** - MySQL database with multiple tables
- **RESTful API** - Complete API endpoints for all operations
- **Password Hashing** - Bcrypt for secure password storage
- **Session Management** - LocalStorage for maintaining login state
- **Responsive Design** - Mobile-friendly with MUT colors (Burgundy #8B0000, Gold #FFD700)
- **Search & Filter** - Real-time search and filter for events
- **Pagination** - 6 events per page with navigation
- **Middleware** - Logger, rate limiter, validation, error handling

---

## Requirements

Before running the project, ensure you have the following installed:

| Software | Version | Purpose |
|----------|---------|---------|
| [Node.js](https://nodejs.org/) | v16+ | Backend runtime |
| [MySQL](https://www.mysql.com/) | v8+ | Database |
| [XAMPP](https://www.apachefriends.org/) | Latest | MySQL and phpMyAdmin |
| Web Browser | Chrome/Firefox | Frontend |

---

## Project Setup

### Step 1: Extract or Clone the Project

If you received a ZIP file, extract it to:
C:\xampp\htdocs\mut-ict-website

text

Or clone using Git:
```bash
git clone https://github.com/yourusername/mut-ict-website.git
Final path should be: C:\xampp\htdocs\mut-ict-website

Step 2: Install Node.js Dependencies
Open a terminal (Command Prompt or PowerShell) in the project folder and run:

bash
cd C:\xampp\htdocs\mut-ict-website
npm install
This installs:

express (web framework)

mysql2 (MySQL driver)

bcryptjs (password hashing)

cors (cross-origin requests)

exceljs (Excel export)

pdfkit (PDF export)

Database Setup
Step 3: Start MySQL
Open XAMPP Control Panel

Click Start next to MySQL

Ensure MySQL shows a green indicator

Step 4: Create Database
Open your browser and go to:

text
http://localhost/phpmyadmin
Click New on the left sidebar

Create database named:

text
mut_ict_db
Select utf8mb4_general_ci as collation

Click Create

Step 5: Create Tables
Open the SQL tab in phpMyAdmin and run each of these queries one by one:

Users Table
sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    surname VARCHAR(100) NOT NULL,
    student_number VARCHAR(50) UNIQUE,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(20),
    password VARCHAR(255) NOT NULL,
    user_type ENUM('admin', 'student', 'external') DEFAULT 'student',
    is_active TINYINT(1) DEFAULT 1,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
Events Table
sql
CREATE TABLE events (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    description TEXT,
    event_date DATE NOT NULL,
    location VARCHAR(200),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
Consultations Table
sql
CREATE TABLE consultations (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    student_name VARCHAR(200) NOT NULL,
    lecturer_name VARCHAR(100) NOT NULL,
    consultation_date DATE NOT NULL,
    consultation_time VARCHAR(10) NOT NULL,
    module VARCHAR(100) NOT NULL,
    reason TEXT NOT NULL,
    status ENUM('pending', 'confirmed', 'completed', 'cancelled') DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
Announcements Table
sql
CREATE TABLE announcements (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    content TEXT NOT NULL,
    category ENUM('general', 'academic', 'events', 'important', 'deadline') DEFAULT 'general',
    priority ENUM('low', 'medium', 'high', 'urgent') DEFAULT 'medium',
    is_active TINYINT(1) DEFAULT 1,
    created_by INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (created_by) REFERENCES users(id) ON DELETE SET NULL
);
Chatbot Tables
sql
CREATE TABLE chatbot_quick_responses (
    id INT AUTO_INCREMENT PRIMARY KEY,
    trigger_keywords TEXT NOT NULL,
    response TEXT NOT NULL,
    category VARCHAR(100),
    priority INT DEFAULT 0,
    is_active TINYINT(1) DEFAULT 1,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE chatbot_conversations (
    id INT AUTO_INCREMENT PRIMARY KEY,
    session_id VARCHAR(100) NOT NULL,
    user_id INT,
    user_message TEXT NOT NULL,
    bot_response TEXT NOT NULL,
    intent VARCHAR(100),
    confidence FLOAT DEFAULT 0,
    source ENUM('chat', 'suggestion', 'faq', 'quick_reply') DEFAULT 'chat',
    user_satisfied TINYINT(1),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
Step 6: Insert Admin User
First, generate a password hash. Open a terminal and run:

bash
node -e "console.log(require('bcryptjs').hashSync('Ncobela@05', 10))"
Copy the output (it will look like $2a$10$...).

Then run this SQL in phpMyAdmin (replace YOUR_HASH_HERE with the hash you just generated):

sql
INSERT INTO users (first_name, surname, student_number, email, password, user_type) 
VALUES ('Admin', 'Ncobela', 'ADMIN001', 'admin@ict.com', 'YOUR_HASH_HERE', 'admin');
Running the Project
Step 7: Start the Backend Server
Open a terminal in the project folder and run:

bash
node api.js
You should see:

text
✅ MySQL Connected on port 3306
✅ API Server running on http://localhost:5000
📝 Test: http://localhost:5000/test
🌐 Access your website via: http://localhost:8080
Leave this terminal window open - the server needs to keep running.

Step 8: Start Apache (for Frontend)
Open XAMPP Control Panel

Click Start next to Apache

Ensure Apache shows a green indicator

Step 9: Access the Website
Open your browser and go to:

text
http://localhost/mut-ict-website/index.html
Or if Apache uses port 8080:

text
http://localhost:8080/mut-ict-website/index.html
Login Instructions
⚠️ IMPORTANT: How to Login as Admin
When logging in as admin, you MUST use the MUT Student login form, NOT the External User form.

Why? The admin account is stored with a student_number in the database, not an email address.

Admin Login Steps:
Go to: http://localhost/mut-ict-website/login.html

Click "MUT Student" button at the top

Enter credentials:

Field	Value
Student Number	ADMIN001
Password	Ncobela@05
Click "Login as Student"

❌ What NOT to do:
Do NOT select "External User" for admin login

Do NOT enter admin email in the email field

Test Student Account
Register a new account at register.html and login using either:

MUT Student form (using your student number)

External User form (using your email address)

API Endpoints
Method	Endpoint	Description	Access
POST	/api/login	User login	Public
GET	/api/events	Get all events	Public
POST	/api/events	Create event	Admin only
PUT	/api/events/:id	Update event	Admin only
DELETE	/api/events/:id	Delete event	Admin only
POST	/api/consultations	Book consultation	Authenticated
Project Structure
text
mut-ict-website/
│
├── api.js                 # Main backend server (Node.js/Express)
├── middleware.js          # Custom middleware functions
├── package.json           # Node.js dependencies
│
├── HTML Files
│   ├── index.html         # Homepage
│   ├── login.html         # Login page
│   ├── register.html      # Registration page
│   ├── profile.html       # User profile page
│   ├── upcoming-events.html # Events listing
│   ├── manage-events.html # Admin event management
│   ├── consultation.html  # Book consultation
│   ├── chatbot.html       # AI assistant
│   ├── admin-dashboard.html # Admin analytics
│   ├── export-reports.html # Export data
│   └── contact.html       # Contact page
│
├── CSS Files
│   ├── login.css
│   ├── events.css
│   ├── consultation.css
│   └── style.css
│
├── img/                   # Images folder
│   └── mutLogo.jpg
│
└── node_modules/          # Node.js packages (generated by npm install)
Troubleshooting
"Cannot connect to server" error
Make sure you ran node api.js in a terminal

The terminal window must stay open

Check if port 5000 is being used by another program

MySQL Connection Error
Ensure MySQL is running in XAMPP (green indicator)

Verify database name is mut_ict_db

Check username/password in api.js (default: root, empty password)

"Invalid credentials" for admin login
Make sure you selected "MUT Student" not "External User"

Verify admin user exists: SELECT * FROM users WHERE student_number = 'ADMIN001'

Password is Ncobela@05 (case-sensitive)

Port 5000 already in use
Change the port in api.js:

javascript
const PORT = 5001;  // Change to any available port
Apache or MySQL won't start
Skype or other programs may be using port 80/3306

Stop conflicting programs or change ports in XAMPP settings

Technologies Used
Layer	Technology
Frontend	HTML5, CSS3, JavaScript
Backend	Node.js, Express.js
Database	MySQL
Authentication	Bcrypt.js, LocalStorage
PDF Export	PDFKit
Excel Export	ExcelJS
Server	XAMPP (Apache + MySQL)
Developer Notes
The system uses localStorage for session management (not PHP sessions)

All passwords are hashed using bcrypt before storing in MySQL

Admin routes are protected using custom middleware

Keep the terminal with node api.js running at all times while using the website

Both Apache (for HTML/CSS/JS) AND Node.js (for API) must be running simultaneously