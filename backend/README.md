# IIITNR Attendance System - Backend

The REST API server that powers the Faculty Portal and Student App. Built with Node.js, Express, and Firebase Admin SDK.

## Features
- **Authentication**: Firebase Authentication middleware.
- **Role-Based Access**: Distinguishes between Faculty (create sessions) and Students (mark attendance).
- **Geofencing**: Validates student coordinates against campus/classroom boundaries.
- **Dynamic QR**: Generates and validates signed, time-limited QR codes.
- **Attendance Logic**: Handles marking, duplicate checks, and attendance history.

## Tech Stack
- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: Firebase Firestore
- **Auth**: Firebase Auth

## Setup
1.  Clone the repository:
    ```bash
    git clone https://github.com/kydrahul/iiitnrattendance.git
    cd iiitnrattendance
    ```
2.  Install dependencies:
    ```bash
    npm install
    ```
3.  Setup Environment:
    - Place your `firebase-service-account.json` in the root.
    - Create a `.env` file (optional if using defaults).
4.  Run Server:
    ```bash
    npm run dev
    # or
    node server.js
    ```

## API Endpoints
- `POST /api/faculty/generate-qr`: Create a new session with QR.
- `POST /api/student/scan-qr`: Mark attendance.
- `POST /api/student/verify-location`: Check geofence status.
