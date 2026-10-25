# Disc Golf South Africa

A membership and event registration web app for Disc Golf South Africa (DGSA). Players can browse events, create an account, buy a membership, register for tournaments and upload proof of payment. Admins manage events, members, registrations and payments.

**Live site:** https://discgolfsummative-7cf2e4163c94.herokuapp.com/

> This is a student project from Open Window.

## Features

**Public**
- Landing page with the featured tournament, upcoming events carousel, FAQ and footer
- Sign up and login (cookie-based JWT authentication)

**Members**
- Member dashboard and profile
- Membership tiers (free or DGSA member) with a payment step
- Tournament registration for 1 to 8 players, with division, DGSA membership ID lookup and proof-of-payment upload

**Admins**
- Admin dashboard for members, registrations and payments
- Create events with a poster and PDGA tier
- Update payment status (`pending`, `on_time`, `overdue`)

## Tech stack

| Layer | Technology |
| --- | --- |
| Frontend | React 19 (Create React App), React Router 6, React Bootstrap, react-helmet-async |
| Backend | Node 22, Express 5, Mongoose 9 |
| Database | MongoDB |
| Auth and security | JWT in cookies, bcryptjs, helmet (CSP), CORS, express-rate-limit, multer |
| Hosting | Heroku (one web process serves the API and the React build) |
| Analytics and SEO | Google Analytics 4, Google Search Console |

## Project structure

backend/
  src/
    config/        Database connection, constants, admin account setup
    controllers/   Route handlers (auth, users, events, registrations, payments, dashboard)
    middleware/    Authentication, uploads, error handling
    models/        User, Event, Registration, Payment, Counter, Upload
    routes/        Express routers mounted under /api
    scripts/       One-off scripts (ensureChampionship.js)
    utils/         Validators, helpers, upload storage
    app.js         Express app, sitemap, serves the React build in production
    server.js      Entry point (env checks, DB connection, admin account)
    seed.js        Development sample data
frontend/
  public/          index.html, robots.txt, logos, manifest
  src/
    assets/        Components and images
    context/       AuthContext
    pages/         Auth, dashboard and registration pages
    services/      API helpers
    Styling/       Page-specific CSS
    App.js         Routes, landing page and per-route SEO tags
Procfile           web: node backend/src/server.js
## Requirements

- Node.js 22.x
- npm
- A MongoDB database (local or MongoDB Atlas)

## Getting started

### 1. Install dependencies

npm install --prefix backend
npm install --prefix frontend

### 2. Configure the backend

Create `backend/.env`:

MONGODB_URI=your-mongodb-connection-string
JWT_SECRET=a-random-string-of-at-least-32-characters
CLIENT_URL=http://localhost:3000
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=a-password-longer-than-8-characters
PORT=5000
NODE_ENV=development

The server will not start if `MONGODB_URI`, `JWT_SECRET`, `CLIENT_URL`, `ADMIN_EMAIL` or `ADMIN_PASSWORD` is missing, or if `JWT_SECRET` is shorter than 32 characters. On every start the admin account for `ADMIN_EMAIL` is created or updated.

### 3. Configure the frontend (optional)

Create `frontend/.env`:

REACT_APP_API_URL=http://localhost:5000
REACT_APP_GA_ID=G-XXXXXXXXXX

`REACT_APP_API_URL` defaults to `http://localhost:5000` in development and to the site's own origin in production. `REACT_APP_GA_ID` is your GA4 Measurement ID and is inserted into `index.html` when the app is built.

### 4. Run locally

Terminal 1 (API on http://localhost:5000):
npm run dev --prefix backend

Terminal 2 (React app on http://localhost:3000):
npm start --prefix frontend

### 5. Seed sample data (development only)

Add `SEED_ADMIN_EMAIL` and `SEED_PASSWORD` to `backend/.env`, then run:

npm run seed --prefix backend

**Warning:** the seed script deletes all existing users, events, payments and registrations first. It refuses to run when `NODE_ENV=production`.

## Scripts

| Command | Description |
| --- | --- |
| `npm start` (root) | Start the production server |
| `npm run dev --prefix backend` | Start the API in Node watch mode |
| `npm run seed --prefix backend` | Load sample data (development only) |
| `npm start --prefix frontend` | Start the React dev server |
| `npm run build --prefix frontend` | Create a production build in `frontend/build/` |
| `npm test --prefix frontend` | Run the frontend tests |

## Frontend routes

| Route | Access | Description |
| --- | --- | --- |
| `/` | Public | Landing page (tournament, events, FAQ) |
| `/login`, `/signup` | Public | Authentication |
| `/membership` | Logged in | Choose a tier and pay for membership (`noindex`) |
| `/registration?eventId=` | Logged in | Tournament registration flow |
| `/dashboard` | Logged in | Member or admin dashboard, depending on role |
| `/dashboard/profile` | Logged in | Profile |
| `/dashboard/payments` | Admin | Payment management |
| `/dashboard/events/new` | Admin | Create an event |

## API overview

All endpoints are under `/api`.

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| POST | `/auth/signup`, `/auth/login` | Public (rate limited) | Create an account or log in |
| POST | `/auth/logout` | Public | Log out |
| GET | `/auth/me` | Optional | Current user |
| GET | `/events`, `/events/:id` | Public | List events or get one |
| POST | `/events` | Admin | Create an event (poster upload) |
| POST | `/events/:eventId/registrations` | Logged in | Register players (proof upload) |
| GET | `/users/lookup?email=` | Logged in | Look up a member by email |
| POST | `/users/membership`, `/users/membership/purchase` | Logged in | Submit or purchase a membership |
| GET | `/registrations/mine` | Logged in | Your registrations |
| GET | `/payments/:id/proof` | Logged in | Download a payment proof |
| GET | `/dashboard/member` | Logged in | Member dashboard data |
| GET | `/admin/payments`, `/admin/members`, `/admin/registrations` | Admin | Admin lists |
| PATCH | `/admin/payments/:id/status` | Admin | Update a payment's status |

Uploads accept PDF, PNG and JPEG for proofs, and PNG, JPEG and WebP for posters, up to 5 MB each. Login and signup are limited to 20 requests per 15 minutes.

## Deployment (Heroku)

The root `package.json` runs `heroku-postbuild`, which installs both apps and builds the React frontend. The `Procfile` starts the backend, which serves the API and the built frontend.

Set these config vars on Heroku:

| Variable | Purpose |
| --- | --- |
| `NODE_ENV=production` | Enables serving the React build and the sitemap |
| `MONGODB_URI`, `JWT_SECRET`, `ADMIN_EMAIL`, `ADMIN_PASSWORD` | Required by the server |
| `CLIENT_URL` | The deployed site URL (also the sitemap base URL) |
| `REACT_APP_GA_ID` | GA4 Measurement ID (must be set before the build runs) |
| `PUBLIC_URL` | Optional override for the sitemap base URL |

## SEO and analytics

- **Metadata:** `frontend/public/index.html` holds the default title, description, canonical and Open Graph tags. `App.js` overrides them per route with react-helmet-async, and `/membership` is marked `noindex, nofollow`.
- **Sitemap:** `GET /sitemap.xml` is generated by `backend/src/app.js` in production.
- **robots.txt:** allows crawling, blocks `/admin` and `/api`, and links to the sitemap.
- **Google Analytics 4:** the gtag snippet in `index.html` records page views and engagement. A custom `sign_up` event (method: email) is sent after a successful signup.

## Security notes

- Never commit `.env` files or credentials. Rotate any secret that has been shared or committed.
- Payment proofs are not served statically; they are only available to authorised users through `/api/payments/:id/proof`.

## Github Repo's
- Heroku: https://github.com/251142-coenraadjlindholm/DiscGolfProject.git
- AWS: https://github.com/251142-coenraadjlindholm/DiscGolf---Copy-AWS.git
