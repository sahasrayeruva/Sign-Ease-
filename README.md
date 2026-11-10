# SignEase – Sign Language Translator

## Overview

SignEase is a web-based sign language learning and translation application designed to help users communicate through hand gestures.

It combines a single-page frontend with a FastAPI backend and MongoDB database. The application uses MediaPipe for real-time hand tracking and a K-Nearest Neighbors (KNN) classifier to recognise hand signs through a webcam.

The machine learning pipeline runs entirely in the browser, so no separate ML server is required.

## Features

- **User Authentication:** Registration, login, and JWT-based authentication.
- **Sign Translator:** Translate selected phrases into corresponding signs.
- **Live Sign Recognition:** Recognise hand gestures through a webcam in real time.
- **Custom Sign Training:** Record and label custom hand-sign samples.
- **Translation History:** Store and manage previous translations.
- **Favorites:** Save frequently used signs.
- **Learning Progress:** Track individual learning progress.
- **Dashboard:** View user activity and application statistics.
- **Feedback:** Submit feedback through the application.

## Architecture

SignEase follows a client-server architecture.

### Frontend
- Displays the application interface.
- Captures webcam input.
- Uses MediaPipe HandLandmarker to detect hand movements.
- Extracts 21 hand landmarks (63 numerical values).
- Uses a KNN classifier to predict the sign.
- Stores custom training samples in browser localStorage.

### Backend
- Built using FastAPI.
- Handles user authentication and API requests.
- Uses JWT for secure authentication.
- Performs database operations using Motor.
- Provides REST APIs for translations, favorites, progress, and feedback.

### Database
MongoDB stores user information and application data.

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML, CSS, JavaScript |
| Backend | Python, FastAPI |
| Database | MongoDB |
| Database Driver | Motor |
| Hand Tracking | MediaPipe |
| Classification | K-Nearest Neighbors (KNN) |
| Authentication | JWT, bcrypt |
| Validation | Pydantic |
| API Documentation | Swagger UI |

## Project Structure

```text
signease-backend/
│
├── app/
│   └── main.py
│
├── index.html
├── requirements.txt
├── .env
├── .env.example
├── Procfile
├── render.yaml
├── frontend-integration.js
└── README.md
```

## Installation and Setup

### Prerequisites

- Python 3.10 or higher
- MongoDB Community Server
- MongoDB Compass (optional)
- Google Chrome or Microsoft Edge
- Webcam for live sign recognition

### 1. Clone the Repository

```bash
git clone <repository-url>
cd signease-backend
```

Run the following commands from the inner `signease-backend` folder containing the `app` directory.

### 2. Install Dependencies

```bash
py -m pip install -r requirements.txt
```

### 3. Configure Environment Variables

Create a `.env` file using `.env.example`.

```env
MONGODB_URL=mongodb://localhost:27017
DATABASE_NAME=signeaseDB
JWT_SECRET=your-secret-key
ADMIN_EMAIL=admin@signease.com
ADMIN_PASSWORD=your-password
CORS_ORIGINS=http://127.0.0.1:5510
```

Use strong credentials and keep `.env` out of GitHub.

### 4. Start the Backend

```bash
py -m uvicorn app.main:app --reload --port 8000
```

Backend URL:

`http://127.0.0.1:8000`

Swagger API documentation:

`http://127.0.0.1:8000/docs`

### 5. Start the Frontend

Open another terminal and run:

```bash
py -m http.server 5510
```

Open:

`http://127.0.0.1:5510/index.html`

**Important:** Run the frontend through a local web server instead of opening `index.html` directly.

## API Reference

The backend provides REST API endpoints for managing application data.

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | API health check |
| POST | `/auth/register` | Register a user |
| POST | `/auth/login` | User login |
| GET | `/me` | Get user profile |
| GET | `/translations` | Retrieve translation history |
| POST | `/translations` | Save a translation |
| DELETE | `/translations/{id}` | Delete a translation |
| GET | `/favorites` | Retrieve favorites |
| POST | `/favorites` | Add a favorite |
| DELETE | `/favorites/{name}` | Remove a favorite |
| GET | `/progress` | Get learning progress |
| PUT | `/progress` | Update learning progress |
| POST | `/feedback` | Submit feedback |

Protected endpoints require a valid JWT access token in the Authorization header.

## Database Design

The application uses MongoDB with the database name `signeaseDB`.

| Collection | Purpose |
|---|---|
| `users` | Stores user accounts and authentication details |
| `translations` | Stores translation history |
| `favorites` | Stores users' favorite signs |
| `signs` | Stores sign names and metadata |

Indexes are created during application startup to improve query performance and prevent duplicate user accounts and favorites.

## Authentication and Security

SignEase uses JWT-based authentication.

1. Users register with their name, email, and password.
2. Passwords are hashed using bcrypt before storage.
3. During login, credentials are verified.
4. The backend generates a JWT access token.
5. Protected API requests use the token for authentication.
6. CORS restricts access to configured frontend origins.

## Machine Learning – Sign Recognition

The sign recognition pipeline operates entirely within the browser.

### Recognition Workflow

```text
Webcam Input
     ↓
MediaPipe Hand Detection
     ↓
21 Hand Landmarks
     ↓
Coordinate Normalisation
     ↓
KNN Classifier (K = 5)
     ↓
Predicted Sign
     ↓
Display Result
```

### Implementation

- MediaPipe detects 21 hand landmarks.
- Each landmark contains X, Y, and Z coordinates.
- The coordinates are converted into 63 numerical values.
- Normalisation reduces the effect of hand position and camera distance.
- KNN compares the input with previously recorded samples.
- Majority voting determines the predicted sign.

### Custom Training

Users can train their own signs through the Train Signs page.

1. Start the webcam.
2. Select a sign label.
3. Capture multiple samples.
4. Save the training data.
5. Test recognition using the trained gestures.

Around 20–30 samples per sign are recommended for better recognition.

Training samples are stored in browser localStorage and can be exported or imported as JSON files.


## Deployment

The project includes `Procfile` and `render.yaml` for deployment.

Suggested deployment setup:

- **Database:** MongoDB Atlas
- **Backend:** Render
- **Frontend:** Netlify, Vercel, or another static hosting service

Update the environment variables and frontend API URL before deployment. HTTPS is required for webcam access on deployed websites.

## Known Limitations

- Currently supports static hand gestures.
- Dynamic gestures involving continuous movement are not fully supported.
- Recognition accuracy depends on training data quality and quantity.
- Training samples are stored locally in the browser.
- Focuses primarily on single-hand recognition.
- The Admin dashboard currently uses local demo data.
- The backend is implemented as a single service.

## Future Enhancements

- Implement dynamic sign recognition.
- Improve accuracy using larger training datasets.
- Store training samples in MongoDB.
- Develop complete admin dashboard functionality.
- Add automated backend testing.
- Improve logging and monitoring.
- Deploy the complete application with HTTPS.

## License

This project is developed for educational and academic purposes.
