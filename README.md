# 📹 Apna Video Call — Real-Time Video Conferencing Application

<div align="center">

![React](https://img.shields.io/badge/React-18.2.0-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![Node.js](https://img.shields.io/badge/Node.js-Express-339933?style=for-the-badge&logo=node.js&logoColor=white)
![Socket.io](https://img.shields.io/badge/Socket.io-4.7.3-010101?style=for-the-badge&logo=socket.io&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-Mongoose-47A248?style=for-the-badge&logo=mongodb&logoColor=white)
![WebRTC](https://img.shields.io/badge/WebRTC-Peer--to--Peer-333333?style=for-the-badge&logo=webrtc&logoColor=white)
![MUI](https://img.shields.io/badge/MUI-Material--UI-007FFF?style=for-the-badge&logo=mui&logoColor=white)

**A full-stack, production-ready video conferencing platform — built from scratch without any third-party video SDK.**

[GitHub Repo](https://github.com/Vishal985057/Video-Conferencing.git) · [Report Bug](https://github.com/Vishal985057/Video-Conferencing/issues) · [Connect on LinkedIn](https://www.linkedin.com/in/vishalkumar004)

</div>

---

## 📌 Table of Contents

- [About The Project](#-about-the-project)
- [Key Features](#-key-features)
- [System Architecture](#-system-architecture)
- [Tech Stack & Why I Chose Each](#-tech-stack--why-i-chose-each)
- [How It Works — Under the Hood](#-how-it-works--under-the-hood)
- [WebRTC Signaling Flow](#-webrtc-signaling-flow)
- [API Reference](#-api-reference)
- [Database Schema](#-database-schema)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
- [Environment Variables](#-environment-variables)
- [Challenges I Solved](#-challenges-i-solved)
- [What I Learned](#-what-i-learned)
- [Future Improvements](#-future-improvements)
- [Author](#-author)

---

## 🚀 About The Project

**Apna Video Call** is a full-stack real-time video conferencing application similar to Google Meet or Zoom — built entirely from scratch without any third-party video SDK like Twilio, Agora, or Daily.co.

The entire video pipeline is built using **raw WebRTC browser APIs**, with **Socket.io** handling the signaling layer, and **Node.js + MongoDB** powering authentication and user history on the backend.

> **Why build this instead of using a video SDK?**
> Any developer can call a paid API. Building the underlying WebRTC signaling, peer connection management, ICE candidate exchange, and stream handling from scratch demonstrates a deep understanding of how real-time communication actually works — the same technology powering Google Meet, Microsoft Teams, and Discord.

---

## ✨ Key Features

| Feature | Description |
|---|---|
| 🎥 **Real-Time Video Calls** | Multi-party peer-to-peer video using raw WebRTC — no SDK |
| 🎙️ **Audio Controls** | Toggle microphone on/off mid-call |
| 📷 **Camera Controls** | Toggle camera on/off mid-call with graceful black-frame fallback |
| 🖥️ **Screen Sharing** | Share your screen in real time using `getDisplayMedia` API |
| 💬 **In-Call Chat** | Real-time chat with message history for users who join mid-call |
| 🔔 **Unread Message Badge** | Chat icon badge showing unread message count |
| 🔐 **User Authentication** | Secure register/login with bcrypt password hashing and token-based auth |
| 📜 **Meeting History** | Every meeting you join is saved and viewable on a history page |
| 👤 **Guest Access** | Join a call without an account via the landing page |
| 📱 **Responsive UI** | Works across desktop and mobile screen sizes |

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        USER'S BROWSER                           │
│                                                                 │
│   React App (localhost:3000)                                    │
│   ┌──────────┐  ┌──────────┐  ┌────────────┐  ┌────────────┐  │
│   │ Landing  │  │   Auth   │  │    Home    │  │  VideoMeet │  │
│   │  Page    │  │  Page    │  │    Page    │  │  Component │  │
│   └──────────┘  └──────────┘  └────────────┘  └─────┬──────┘  │
│                                                      │          │
│              AuthContext (Global State)              │          │
│                    │                                 │          │
└────────────────────┼─────────────────────────────────┼──────────┘
                     │ HTTP REST (Axios)                │ WebSocket (Socket.io)
                     ▼                                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                    NODE.JS SERVER (localhost:8000)               │
│                                                                 │
│   ┌──────────────────────┐    ┌───────────────────────────┐    │
│   │   Express REST API   │    │   Socket.io Real-Time     │    │
│   │                      │    │                           │    │
│   │  POST /login         │    │  join-call  → room mgmt   │    │
│   │  POST /register      │    │  signal     → SDP/ICE     │    │
│   │  POST /add_activity  │    │  chat-msg   → broadcast   │    │
│   │  GET  /get_activity  │    │  disconnect → cleanup     │    │
│   └──────────┬───────────┘    └───────────────────────────┘    │
│              │                                                  │
└──────────────┼──────────────────────────────────────────────────┘
               │ Mongoose ODM
               ▼
┌──────────────────────────────┐
│   MongoDB Atlas (Cloud DB)   │
│                              │
│   users collection           │
│   meetings collection        │
└──────────────────────────────┘

               ↕  After signaling: Direct P2P
┌──────────────────────────────┐
│  Browser A  ◄────────►  Browser B  (WebRTC — NO server involved)  │
└──────────────────────────────┘
```

**The key architectural insight:** The server is never in the video path. Once two browsers exchange their network info (via the Socket.io signaling server), video flows **directly browser-to-browser** over WebRTC. This is how the app handles multiple concurrent users without the server becoming a bottleneck.

---

## 🛠️ Tech Stack & Why I Chose Each

### Frontend

| Technology | Version | Why I Used It |
|---|---|---|
| **React** | 18.2.0 | Component-based UI with efficient re-renders via virtual DOM. `useState` / `useEffect` / `useRef` / `useContext` hooks used throughout. |
| **Socket.io Client** | 4.7.3 | Persistent WebSocket connection for real-time signaling and chat. Handles reconnection automatically. |
| **Material UI (MUI)** | 5.15.4 | Production-grade component library with responsive grid, icons, and accessible components. |
| **React Router DOM** | 6.21.1 | Client-side routing with dynamic route matching (`/:url` for meeting rooms). |
| **Axios** | 1.6.5 | HTTP client with interceptors, base URL configuration, and cleaner error handling than native `fetch`. |

### Backend

| Technology | Version | Why I Used It |
|---|---|---|
| **Node.js + Express** | 4.18.2 | Non-blocking I/O ideal for concurrent connections. Minimal boilerplate with full control over the request/response cycle. |
| **Socket.io Server** | 4.7.3 | Event-driven WebSocket layer for the WebRTC signaling server. Handles rooms, namespaces, and disconnection cleanup. |
| **MongoDB + Mongoose** | 8.0.3 | Schema-based modelling on a flexible document store. Ideal for user profiles and meeting history records. |
| **bcrypt** | 5.1.1 | Industry-standard password hashing with salt rounds. Passwords are never stored in plain text. |
| **http-status** | 1.7.3 | Semantic HTTP status codes for readable, self-documenting API responses. |

### Core Browser APIs (No SDK)

| API | Purpose |
|---|---|
| **WebRTC** (`RTCPeerConnection`) | Establishes direct peer-to-peer video/audio connections |
| **`getUserMedia`** | Requests camera and microphone access |
| **`getDisplayMedia`** | Screen sharing |
| **`RTCSessionDescription`** | SDP offer/answer exchange for codec negotiation |
| **`RTCIceCandidate`** | Network path discovery for NAT traversal |
| **Canvas API** | Generates a black video frame when camera is off |
| **Web Audio API** | Generates a silent audio track when mic is off |

---

## ⚙️ How It Works — Under the Hood

### 1. Authentication Flow

```
User submits login form
        │
        ▼
Frontend (AuthContext.jsx) sends POST /api/v1/users/login
        │
        ▼
Backend looks up user in MongoDB by username
        │
        ├── Not found → 404 response
        │
        └── Found → bcrypt.compare(plainPassword, hashedPassword)
                        │
                        ├── Wrong → 401 Unauthorized
                        │
                        └── Correct → crypto.randomBytes(20) generates token
                                      Token saved to user's DB record
                                      Token returned to browser
                                      Browser stores in localStorage
```

Every subsequent request sends this token to identify the user — no passwords are ever stored in the browser.

### 2. Video Call Flow

```
User types meeting code → clicks Join
        │
        ▼
addToUserHistory(code) — saves to MongoDB
        │
        ▼
navigate("/abc123") — URL becomes the room ID
        │
        ▼
VideoMeet component loads → getPermissions() → camera/mic popup
        │
        ▼
User enters username → clicks Connect
        │
        ├── getMedia() → getUserMedia() → camera stream ready
        │
        └── connectToSocketServer() → io.connect(server)
                │
                └── emit("join-call", window.location.href)
                        │
                        ▼
                Server: adds socket to room, notifies ALL members
                        │
                        ▼
                Each browser: new RTCPeerConnection() per member
                        │
                        ▼
                Offer → Signal → Answer → ICE candidates
                        │
                        ▼
                Direct P2P video stream established ✓
```

### 3. Why `useRef` instead of `useState` for the socket and peer connections

`useState` triggers a React re-render every time it changes. If the socket connection or peer connections were stored in state, every time a new person joined the call, React would re-render and potentially reset those connections. `useRef` holds a mutable value that persists across renders without causing them — perfect for long-lived objects like WebSocket connections and WebRTC peer connections.

### 4. The "Black Silence" Pattern

WebRTC requires a media stream to exist even when the user has no camera or has turned it off. An empty connection would break the signaling. Instead, the app generates:

- A **black video track** — using the Canvas API to capture a black rectangle as a video stream
- A **silent audio track** — using the Web Audio API to create an oscillator that is immediately muted

This keeps the WebRTC connection alive while accurately representing the user's actual state (camera off / mic off).

---

## 🔄 WebRTC Signaling Flow

WebRTC is peer-to-peer but needs an initial handshake. This is called **signaling** — the two browsers need to exchange what they can do and how to reach each other. The server acts as a postman for this exchange only.

```
Browser A                    Server                    Browser B
    │                           │                           │
    │── join-call(url) ─────────►│                           │
    │                           │──── user-joined(A, [A]) ──►│
    │◄─── user-joined(A, [A]) ──│                           │
    │                           │                           │
    │── join-call(url) ─────────►│   (Browser B joins)       │
    │                           │──── user-joined(B, [A,B]) ►│
    │◄─── user-joined(B, [A,B]) │                           │
    │                           │                           │
    │  createOffer() [SDP]       │                           │
    │── signal(B, {sdp:offer}) ─►│                           │
    │                           │──── signal(A, {sdp:offer}) ►│
    │                           │   createAnswer() [SDP]     │
    │                           │◄── signal(A, {sdp:answer}) │
    │◄── signal(B, {sdp:answer}) │                           │
    │                           │                           │
    │── signal(B, {ice:...}) ───►│──── signal(A, {ice:...}) ─►│
    │◄── signal(B, {ice:...}) ──│◄─── signal(A, {ice:...}) ──│
    │                           │                           │
    │◄═══════════ Direct P2P Video/Audio (NO server) ════════►│
```

After the ICE candidates are exchanged, all media flows **directly** between the two browsers. The server is no longer involved in the media path.

---

## 📡 API Reference

### Base URL
```
http://localhost:8000/api/v1/users
```

### Endpoints

#### `POST /register`
Register a new user.

**Request Body:**
```json
{
  "name": "John Doe",
  "username": "johndoe",
  "password": "mypassword"
}
```

**Responses:**
| Status | Meaning |
|---|---|
| `201 Created` | `{ "message": "User Registered" }` |
| `302 Found` | `{ "message": "User already exists" }` |
| `500` | Server error |

---

#### `POST /login`
Authenticate a user and receive a session token.

**Request Body:**
```json
{
  "username": "johndoe",
  "password": "mypassword"
}
```

**Responses:**
| Status | Meaning |
|---|---|
| `200 OK` | `{ "token": "a3f8b2c9..." }` |
| `404 Not Found` | `{ "message": "User Not Found" }` |
| `401 Unauthorized` | `{ "message": "Invalid Username or password" }` |

---

#### `POST /add_to_activity`
Save a meeting to the user's history.

**Request Body:**
```json
{
  "token": "a3f8b2c9...",
  "meeting_code": "abc123"
}
```

**Responses:**
| Status | Meaning |
|---|---|
| `201 Created` | `{ "message": "Added code to history" }` |

---

#### `GET /get_all_activity`
Retrieve all past meetings for a user.

**Query Parameters:**
```
?token=a3f8b2c9...
```

**Response:**
```json
[
  {
    "_id": "...",
    "user_id": "johndoe",
    "meetingCode": "abc123",
    "date": "2024-01-15T10:30:00.000Z"
  }
]
```

---

### Socket.io Events

| Event | Direction | Payload | Description |
|---|---|---|---|
| `join-call` | Client → Server | `url: string` | Join a room identified by the URL |
| `user-joined` | Server → Client | `id, clients[]` | Notifies room when someone joins |
| `user-left` | Server → Client | `id: string` | Notifies room when someone disconnects |
| `signal` | Bidirectional | `id, message` | Relay SDP offers/answers and ICE candidates |
| `chat-message` | Bidirectional | `data, sender` | Send and receive chat messages |

---

## 🗄️ Database Schema

### Users Collection

```javascript
{
  name:     String,   // required — display name
  username: String,   // required, unique — used for login
  password: String,   // required — bcrypt hashed, never plain text
  token:    String    // optional — set on login, used for API auth
}
```

### Meetings Collection

```javascript
{
  user_id:     String,  // username of who attended
  meetingCode: String,  // required — the room code
  date:        Date     // auto-set to Date.now on save
}
```

---

## 📁 Project Structure

```
root/
├── frontend/
│   ├── public/
│   │   ├── index.html          # Single HTML file — React mounts into <div id="root">
│   │   └── background.png
│   └── src/
│       ├── contexts/
│       │   └── AuthContext.jsx  # Global auth state + all API call functions
│       ├── pages/
│       │   ├── landing.jsx      # Public landing page with nav
│       │   ├── authentication.jsx # Login/Register form (toggled by state)
│       │   ├── home.jsx         # Protected — meeting code input
│       │   ├── history.jsx      # Protected — past meetings list
│       │   └── VideoMeet.jsx    # Core — WebRTC + Socket.io + chat
│       ├── styles/
│       │   └── videoComponent.module.css  # Scoped CSS for VideoMeet
│       ├── utils/
│       │   └── withAuth.jsx     # Higher Order Component for route protection
│       ├── App.js               # Router — maps URLs to page components
│       └── environment.js       # Toggle between dev and production server URL
│
└── backend/
    └── src/
        ├── controllers/
        │   ├── user.controller.js   # login, register, history handlers
        │   └── socketManager.js     # Socket.io: rooms, signaling, chat
        ├── models/
        │   ├── user.model.js        # Mongoose schema for users
        │   └── meeting.model.js     # Mongoose schema for meeting history
        ├── routes/
        │   └── user.routes.js       # Maps URL paths to controller functions
        └── app.js                   # Entry point: Express setup, DB connection, server start
```

---

## 🏁 Getting Started

### Prerequisites
- Node.js v16+
- npm or yarn
- A MongoDB Atlas account (free tier works fine)

### 1. Clone the repository

```bash
git clone https://github.com/yourusername/apna-video-call.git
cd apna-video-call
```

### 2. Set up the Backend

```bash
cd backend
npm install
```

Open `src/app.js` and replace the MongoDB connection string with your own:
```js
await mongoose.connect("mongodb+srv://<username>:<password>@cluster.mongodb.net/<dbname>")
```

Start the backend server:
```bash
npm run dev       # development (with nodemon auto-restart)
# or
npm start         # production
```

Backend runs on `http://localhost:8000`

### 3. Set up the Frontend

```bash
cd frontend
npm install
```

Open `src/environment.js` and set development mode:
```js
let IS_PROD = false;   // use localhost:8000
```

Start the frontend:
```bash
npm start
```

Frontend runs on `http://localhost:3000`

### 4. Try it out

1. Open `http://localhost:3000` in your browser
2. Click **Register** and create an account
3. Log in and enter any meeting code (e.g. `testroom`)
4. Click **Join**
5. Open a second browser window / incognito tab and join the same code
6. You're in a live video call!

---

## 🔧 Environment Variables

For production, move sensitive values out of source code into a `.env` file.

**Backend `.env`:**
```env
PORT=8000
MONGO_URI=mongodb+srv://your_user:your_password@cluster.mongodb.net/yourdb
```

**Frontend `src/environment.js`:**
```js
let IS_PROD = false;   // false = localhost, true = production URL
```

---

## 🧗 Challenges I Solved

### 1. Stale State in Async Callbacks
**Problem:** Inside Socket.io event callbacks, reading React state values always returned the initial value (a stale closure). For example, the `videos` array always appeared empty even when it had items.

**Solution:** Used a `useRef` mirror (`videoRef`) that always holds the current value. Inside the callback, read from `videoRef.current` instead of the state variable. Updating state still triggers re-renders; the ref is just a always-fresh reference.

### 2. WebRTC Requires a Stream Even When Camera Is Off
**Problem:** When a user doesn't have or has turned off their camera/mic, `addStream()` would fail or the connection would break.

**Solution:** Implemented the "black silence" pattern — a black video frame generated from the Canvas API and a silent audio track from the Web Audio API. These provide structurally valid media tracks that satisfy WebRTC while accurately representing no camera/mic.

### 3. Chat History for Late Joiners
**Problem:** When a user joins a call that's already in progress, they couldn't see any messages sent before they arrived.

**Solution:** The server stores all messages in memory per room. When a new socket joins, the server replays all existing messages directly to that socket using `io.to(socket.id).emit(...)`.

### 4. Screen Share → Camera Fallback
**Problem:** When a user stops screen sharing (via browser's native "Stop sharing" button), the call would lose video entirely.

**Solution:** Attached an `onended` event handler to the screen share track. When it fires, `setScreen(false)` is called and `getUserMedia()` is invoked to switch back to the camera stream.

### 5. Concurrent Stream Updates Without Race Conditions
**Problem:** Multiple users joining simultaneously could cause peer connections to be set up out of order, resulting in missing streams.

**Solution:** Used the functional form of `setVideos` (`setVideos(prev => [...prev, newItem])`) which always operates on the latest state value, preventing stale updates from overwriting concurrent changes.

---

## 📚 What I Learned

**WebRTC & Real-Time Communication**
- Deep understanding of the ICE framework (Interactive Connectivity Establishment) and how browsers discover each other across NAT/firewalls
- SDP (Session Description Protocol) — how browsers negotiate codecs, bitrates, and media formats before a call begins
- The distinction between signaling (coordinated via server) and media transport (always peer-to-peer)
- STUN vs TURN servers and when each is needed

**React Patterns**
- When to use `useState` vs `useRef` — the difference between "I need the screen to update" and "I need to store a value"
- The stale closure problem in `useEffect` and event handlers, and how to solve it with refs
- Higher Order Components (HOC) as a clean pattern for cross-cutting concerns like authentication
- Context API for global state without prop drilling

**Backend & Security**
- Stateless authentication with tokens vs session cookies
- Why passwords must be hashed (bcrypt salt rounds and cost factors)
- Express middleware pipeline — how `app.use()` creates a chain of request processors
- CORS — why it exists and what it actually does

**System Design**
- Why the media server architecture (all video through server) doesn't scale — and how peer-to-peer solves it
- The trade-offs between WebSocket and HTTP for different types of data
- How Socket.io rooms map naturally to meeting rooms

---

## 🔮 Future Improvements

- [ ] **TURN server support** — Currently relies only on STUN (Google's free server). TURN servers are needed when users are behind symmetric NAT, which is common in corporate networks. Would use a service like Twilio or self-hosted coturn.
- [ ] **JWT authentication** — Replace the random token approach with signed JWTs that carry expiry information and don't require a database lookup on every request.
- [ ] **Room capacity limits** — WebRTC mesh topology (every peer connects to every other peer) doesn't scale well beyond ~6–8 people. Would need to implement SFU (Selective Forwarding Unit) architecture for larger rooms.
- [ ] **Recording** — Use the `MediaRecorder` API to record the local stream and upload chunks to a storage service.
- [ ] **Virtual backgrounds** — Use TensorFlow.js body segmentation to blur or replace the background.
- [ ] **Waiting room** — Host-controlled entry with admit/deny functionality.
- [ ] **Reactions & hand raise** — Quick reaction buttons sending real-time Socket.io events.
- [ ] **Mobile app** — React Native with the same backend (Socket.io and REST API are platform-agnostic).
- [ ] **End-to-end encryption** — WebRTC already encrypts media (SRTP), but adding E2EE for chat messages using the Web Crypto API.

---

## 👤 Author

**Vishal Kumar**

| | |
|---|---|
| 📧 Email | [vishal37086@gmail.com](mailto:vishal37086@gmail.com) |
| 💼 LinkedIn | [linkedin.com/in/vishalkumar004](https://www.linkedin.com/in/vishalkumar004) |
| 📱 Mobile | +91 8580745871 |
| 💻 GitHub | [github.com/Vishal985057](https://github.com/Vishal985057) |

---

<div align="center">

**If you found this project interesting, please consider giving it a ⭐**

[GitHub Repo](https://github.com/Vishal985057/Video-Conferencing.git)

</div>
