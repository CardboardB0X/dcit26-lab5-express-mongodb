# DCIT 26 - Student Records REST API (Express & MongoDB)

A robust RESTful API for managing student records, built with **Node.js**, **Express**, **MongoDB**, and **Mongoose**. This backend service supports full CRUD operations (Create, Read, Update, Delete) with schema validation, structured JSON responses, and centralized error handling.

---

## Project Architecture

```
lab5-student-api/
├── backend/
│   ├── models/
│   │   └── Student.js        # Mongoose Schema & Student Model
│   └── server.js             # Express application & REST API routes
├── .env                      # Local environment configuration (gitignored)
├── .env.example              # Environment variables template
├── .gitignore                # Git ignore rules
├── package.json              # Project dependencies and npm scripts
└── README.md                 # Project documentation and API reference
```

---

## Features

- **Database-driven Persistence**: Fully persisted storage in MongoDB using Mongoose documents (no temporary in-memory arrays).
- **Graceful Lifecycle**: Express server listens on port only after the MongoDB connection is verified and established.
- **Data Validation & Sanitization**: Strict schema-level validation (e.g. required fields, string trimming, yearLevel integer constraint between 1 and 4).
- **Robust Error Handling**: Standardized HTTP status codes (200, 201, 400, 404, 500) with descriptive JSON messages and no leaked internal stack traces.

---

## Data Model (Student Schema)

| Field | Type | Validation & Constraints | Description |
| :--- | :--- | :--- | :--- |
| `fullName` | `String` | Required, Trimmed | Full name of the student |
| `course` | `String` | Required, Trimmed | Degree / program (e.g., BSIT, BSCS) |
| `yearLevel` | `Number` | Required, Integer, Min: 1, Max: 4 | Academic year level (1 to 4) |
| `section` | `String` | Optional, Trimmed | Assigned class section (e.g., IT-2A) |
| `createdAt` | `Date` | Default: current timestamp | Timestamp when record was created |

---

## Prerequisites & Setup

### 1. Prerequisites
- **Node.js** (v18.x or later recommended)
- **npm** (v9.x or later)
- **MongoDB Community Server** (running locally on port `27017` or a MongoDB Atlas connection string)
- **MongoDB Compass** or **mongosh** (for inspecting database collections)

### 2. Clone the Repository
```bash
git clone https://github.com/CardboardB0X/DCIT-26_LAB5_Express_and_MongoDB.git lab5-student-api
cd lab5-student-api
```

### 3. Install Dependencies
```bash
npm install
```

### 4. Configure Environment Variables
Create a `.env` file in the project root by copying `.env.example`:

```bash
cp .env.example .env
```

Contents of `.env`:
```env
PORT=5000
MONGODB_URI=mongodb://127.0.0.1:27017/lab5_students
```

> **Security Warning**: The `.env` file contains local secrets and configurations. It is added to `.gitignore` and must never be committed to git.

---

## Running the Server

### Development Mode (with Nodemon hot-reloading)
```bash
npm run dev
```

### Production Mode
```bash
npm start
```

Upon successful startup, the console will display:
```
Connected successfully to MongoDB at mongodb://127.0.0.1:27017/lab5_students
Server is running and listening on port 5000
```

---

## API Endpoints

| Method | Endpoint | Description | Status Codes |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/health` | Service health status check | `200` |
| `GET` | `/api/students` | Get all student records | `200`, `500` |
| `GET` | `/api/students/:id` | Get single student record by ID | `200`, `400`, `404`, `500` |
| `POST` | `/api/students` | Create a new student record | `201`, `400`, `500` |
| `PUT` | `/api/students/:id` | Update an existing student record | `200`, `400`, `404`, `500` |
| `DELETE` | `/api/students/:id` | Delete a student record by ID | `200`, `400`, `404`, `500` |

---

## Request & Response Examples

### 1. Health Check
- **Request:** `GET /api/health`
- **Response:** `200 OK`
```json
{
  "status": "UP",
  "message": "Student Records REST API is healthy and operational",
  "timestamp": "2026-10-28T10:45:00.000Z"
}
```

---

### 2. Create Student Record
- **Request:** `POST /api/students`
- **Headers:** `Content-Type: application/json`
- **Body:**
```json
{
  "fullName": "Alex Santos",
  "course": "BSIT",
  "yearLevel": 2,
  "section": "IT-2A"
}
```
- **Response:** `201 Created`
```json
{
  "_id": "671fb2a3c7482d8a01f5e821",
  "fullName": "Alex Santos",
  "course": "BSIT",
  "yearLevel": 2,
  "section": "IT-2A",
  "createdAt": "2026-10-28T10:46:15.123Z"
}
```

---

### 3. Get All Students
- **Request:** `GET /api/students`
- **Response:** `200 OK`
```json
[
  {
    "_id": "671fb2a3c7482d8a01f5e821",
    "fullName": "Alex Santos",
    "course": "BSIT",
    "yearLevel": 2,
    "section": "IT-2A",
    "createdAt": "2026-10-28T10:46:15.123Z"
  }
]
```

---

### 4. Get Student by ID
- **Request:** `GET /api/students/671fb2a3c7482d8a01f5e821`
- **Response:** `200 OK`
```json
{
  "_id": "671fb2a3c7482d8a01f5e821",
  "fullName": "Alex Santos",
  "course": "BSIT",
  "yearLevel": 2,
  "section": "IT-2A",
  "createdAt": "2026-10-28T10:46:15.123Z"
}
```
- **Error Response (Invalid ID):** `400 Bad Request`
```json
{
  "message": "Invalid student ID format"
}
```
- **Error Response (Not Found):** `404 Not Found`
```json
{
  "message": "Student record not found"
}
```

---

### 5. Update Student Record
- **Request:** `PUT /api/students/671fb2a3c7482d8a01f5e821`
- **Headers:** `Content-Type: application/json`
- **Body:**
```json
{
  "yearLevel": 3,
  "section": "IT-3A"
}
```
- **Response:** `200 OK`
```json
{
  "_id": "671fb2a3c7482d8a01f5e821",
  "fullName": "Alex Santos",
  "course": "BSIT",
  "yearLevel": 3,
  "section": "IT-3A",
  "createdAt": "2026-10-28T10:46:15.123Z"
}
```

---

### 6. Delete Student Record
- **Request:** `DELETE /api/students/671fb2a3c7482d8a01f5e821`
- **Response:** `200 OK`
```json
{
  "message": "Student record deleted successfully",
  "student": {
    "_id": "671fb2a3c7482d8a01f5e821",
    "fullName": "Alex Santos",
    "course": "BSIT",
    "yearLevel": 3,
    "section": "IT-3A",
    "createdAt": "2026-10-28T10:46:15.123Z"
  }
}
```

---

## Testing with cURL

```bash
# 1. Health check
curl -X GET http://localhost:5000/api/health

# 2. Create student
curl -X POST http://localhost:5000/api/students \
  -H "Content-Type: application/json" \
  -d "{\"fullName\":\"Alex Santos\",\"course\":\"BSIT\",\"yearLevel\":2,\"section\":\"IT-2A\"}"

# 3. List all students
curl -X GET http://localhost:5000/api/students

# 4. Get by ID
curl -X GET http://localhost:5000/api/students/<STUDENT_ID>

# 5. Update student
curl -X PUT http://localhost:5000/api/students/<STUDENT_ID> \
  -H "Content-Type: application/json" \
  -d "{\"yearLevel\":3,\"section\":\"IT-3A\"}"

# 6. Delete student
curl -X DELETE http://localhost:5000/api/students/<STUDENT_ID>
```

---

## Validation & Error Response Standards

| Status Code | Scenario | Sample Response |
| :--- | :--- | :--- |
| **`400 Bad Request`** | Malformed ObjectId or schema validation error (e.g. missing required field, `yearLevel` outside 1-4) | `{"message": "Validation failed", "errors": ["Year level must be between 1 and 4"]}` |
| **`404 Not Found`** | Valid ObjectId but document does not exist | `{"message": "Student record not found"}` |
| **`500 Internal Server Error`** | Unexpected server-side failure (stack trace safely omitted) | `{"message": "Internal server error"}` |

---

## License
Educational project for DCIT 26.
