# Disc Golf South Africa

Disc Golf South Africa is a full-stack membership and event registration
application. The frontend is built with React and React Bootstrap, and the
backend is an Express API backed by MongoDB.

## Features

- Public landing page with tournament and upcoming event information
- Member sign-up, login, profile, and membership purchase flows
- Tournament registration and payment-proof upload
- Member dashboard
- Admin dashboard for managing members, events, registrations, and payments
- Responsive styling for desktop and mobile screens

## Project structure

```text
backend/    Express API, MongoDB models, authentication, and uploads
frontend/   React application and styling
uploads/    Uploaded payment proofs and event posters
Procfile    Production start command
```

## Requirements

- Node.js 22 or a compatible current Node.js release
- npm
- MongoDB

## MongoDB configuration

Create `backend/.env` with your normal MongoDB connection and application
settings:

```env
MONGODB_URI=your-mongodb-connection-string
JWT_SECRET=your-secret-at-least-32-characters-long
CLIENT_URL=http://localhost:3000
ADMIN_EMAIL=your-admin-email
ADMIN_PASSWORD=your-admin-password
PORT=5000
NODE_ENV=development
```

Use a long, unique value for `JWT_SECRET`. The application connects to the
MongoDB database specified by `MONGODB_URI`; create and manage that database
through your regular MongoDB setup. Do not commit `.env` files or credentials
to the repository.

The frontend uses `http://localhost:5000` for the API during development. To
use another API URL, create `frontend/.env` and set:

```env
REACT_APP_API_URL=http://localhost:5000
```

## Installation

Install dependencies in both application directories:

```bash
npm install --prefix backend
npm install --prefix frontend
```

## Running locally

Start the backend in one terminal:

```bash
npm run dev --prefix backend
```

Start the frontend in a second terminal:

```bash
npm start --prefix frontend
```

The frontend is available at [http://localhost:3000](http://localhost:3000).
The API runs at [http://localhost:5000](http://localhost:5000) by default.

## Available frontend scripts

Run these commands from the `frontend` directory, or use the `--prefix
frontend` form from the repository root:

| Command | Description |
| --- | --- |
| `npm start` | Start the React development server |
| `npm test` | Run the test suite in watch mode |
| `npm run build` | Create an optimized production build in `build/` |

## Available backend scripts

| Command | Description |
| --- | --- |
| `npm start --prefix backend` | Start the production API server |
| `npm run dev --prefix backend` | Start the API with Node watch mode |

## Production build

From the repository root, install dependencies and build the frontend:

```bash
npm install --prefix backend
npm install --prefix frontend
npm run build --prefix frontend
```

The root `Procfile` starts the backend:

```text
web: node backend/src/server.js
```

In production, configure `NODE_ENV=production`, set `CLIENT_URL` to the
deployed frontend URL, and provide the production MongoDB connection string
and authentication credentials through the hosting provider's environment
settings.

## Styling convention

Page-specific CSS classnames use a camelCase component prefix followed by a
descriptive role and the `Page` suffix. For example:

```jsx
<main className="memberPaymentLayoutPage">
  <h1 className="memberPaymentTitlePage">Become a Member</h1>
</main>
```

Keep React classnames and their corresponding CSS selectors synchronized when
renaming or adding UI elements.
