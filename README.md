# 🍽️ FoodieHub

**Discover food. Share your passion.**

FoodieHub is a full-stack, short-video platform for food lovers. Viewers can browse a feed of food videos, like and save their favourites, and explore creator profiles, while creators get a dedicated dashboard to upload videos and track their likes.

**Live Demo:** [foodie-hub-puce.vercel.app](https://foodie-hub-puce.vercel.app)

> **Note:** The backend runs on a free Render instance and may take a short while to wake up. The app shows a "Start Server" banner that pings the `/health` endpoint; wait for it to respond, then return to the app.

---

## Features

### For Viewers
- Register / log in with secure, cookie-based authentication
- Browse a feed of food videos, newest first
- **Like** and **Save** videos with toggle behaviour (state is remembered per user)
- Dedicated **Saved** page listing all bookmarked videos
- Visit **creator profiles** to see all their uploads and total likes
- **Share** a video using the native Web Share API, with clipboard fallback

### For Creators
- Separate creator registration and login flow
- **Creator Dashboard** to upload videos (title, description, video file) with a live preview
- Stats for total videos and total likes
- Recent uploads list

### Experience
- Responsive UI built with Tailwind CSS
- Loading skeletons and friendly error states with retry / login actions
- Server wake-up banner for cold starts on the free hosting tier

---

## Tech Stack

| Layer | Technologies |
| --- | --- |
| **Frontend** | React 19, Vite, React Router, Tailwind CSS 4, Axios, Lucide React |
| **Backend** | Node.js, Express 5 |
| **Database** | MongoDB with Mongoose |
| **Auth** | JWT stored in HTTP-only cookies, bcrypt password hashing |
| **Media Storage** | ImageKit (video uploads via Multer in-memory buffers) |
| **Deployment** | Vercel (frontend), Render (backend) |

---

## Architecture

```
React + Vite (Vercel)
        │  Axios (withCredentials)
        ▼
Express REST API (Render) ──► MongoDB (Mongoose)
        │
        └──► ImageKit (video storage / CDN URLs)
```

- Two separate roles, **user** and **creator**, each with their own model, JWT cookie and auth middleware.
- Videos are uploaded through Multer into memory, pushed to ImageKit, and the returned URL is stored in MongoDB.
- Likes and saves are separate collections with a **unique compound index** on `(user, food)` to prevent duplicates.

---

## Project Structure

```
FoodieHub/
├── frontend/
│   ├── src/
│   │   ├── api/            # Axios instance
│   │   ├── components/     # Navbar, ServerWakeupBanner
│   │   ├── pages/          # Home, Feeds, Saved, Profile, CreatorDashboard
│   │   │   └── auth/       # User & creator login/register
│   │   └── router/         # App routes
│   └── vercel.json
└── bAckend/
    ├── server.js
    └── src/
        ├── app.js
        ├── controller/     # auth, foodVideo, creator
        ├── middleware/     # user & creator JWT auth
        ├── models/         # user, creator, foodVideo, likes, save
        ├── routes/
        ├── services/       # ImageKit storage service
        └── db/
```

---

## API Overview

| Method | Endpoint | Description | Auth |
| --- | --- | --- | --- |
| POST | `/auth/user/register` | Register a user | – |
| POST | `/auth/user/login` | Log in a user | – |
| POST | `/auth/user/logout` | Log out a user | – |
| GET | `/auth/user/me` | Get current user | User |
| POST | `/auth/creator/register` | Register a creator | – |
| POST | `/auth/creator/login` | Log in a creator | – |
| POST | `/auth/creator/logout` | Log out a creator | – |
| GET | `/foodvideo` | Get feed with liked/saved status | User / Creator |
| POST | `/foodvideo` | Upload a food video | Creator |
| GET | `/foodvideo/myVideos` | Get the creator's own videos | Creator |
| POST | `/foodvideo/like` | Toggle like | User / Creator |
| POST | `/foodvideo/save` | Toggle save | User / Creator |
| GET | `/foodvideo/getsave` | Get saved videos | User / Creator |
| GET | `/creator/:id` | Get creator profile and videos | User / Creator |
| GET | `/health` | Server health check | – |

---

## Getting Started

### Prerequisites
- Node.js 18+
- A MongoDB database (local or Atlas)
- An [ImageKit](https://imagekit.io) account

### 1. Clone the repository
```bash
git clone https://github.com/amansethiya/FoodieHub.git
cd FoodieHub
```

### 2. Backend setup
```bash
cd bAckend
npm install
```

Create a `.env` file in `bAckend/`:
```env
PORT=3000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret
IMAGEKIT_PUBLIC_KEY=your_imagekit_public_key
IMAGEKIT_PRIVATE_KEY=your_imagekit_private_key
IMAGEKIT_URL_ENDPOINT=your_imagekit_url_endpoint
```

Start the server:
```bash
npm run dev
```

### 3. Frontend setup
```bash
cd frontend
npm install
npm run dev
```

> For local development, update the `baseURL` in `frontend/src/api/axios.js` to `http://localhost:3000`, and the CORS `origin` in `bAckend/src/app.js` to your local frontend URL (e.g. `http://localhost:5173`). Auth cookies are set with `secure: true` and `sameSite: "none"`, so they may need adjusting for plain-HTTP local testing.

---

## Future Improvements
- Comments on videos
- Follow / unfollow creators
- Search and category filters
- Video deletion and editing for creators
- Pagination / infinite scroll for the feed

---

## Author/Developer

**Aman Sethiya**
GitHub: [@amansethiya](https://github.com/amansethiya)
