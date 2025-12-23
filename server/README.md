# Family Hub API (Google Calendar + Firebase friendly)

This Express service provides OAuth 2.0 authentication with Google and a simple endpoint to fetch calendar events for your Family Hub app.

## Setup

1. Copy `.env.example` to `.env` and fill in your Google OAuth credentials.
2. Install dependencies:
   ```bash
   npm install
   ```
3. Run the server:
   ```bash
   npm run dev
   ```

By default the server listens on `http://localhost:3001` and expects your React app to run at `http://localhost:3000`.

## OAuth flow overview

1. Frontend hits `GET /auth/url` to receive the Google consent URL.
2. User completes Google sign-in/consent and Google redirects to `/auth/callback`.
3. The server stores the OAuth tokens in the session.
4. Frontend calls `GET /events` to read upcoming calendar events using the stored tokens.
5. `POST /auth/logout` clears the session.

You can adjust scopes or add Firestore writes (e.g., caching child schedules) inside `src/index.js`.

### Minimal React usage example

```tsx
// inside a React component
const [events, setEvents] = useState([]);

useEffect(() => {
  fetch('http://localhost:3001/auth/url', { credentials: 'include' })
    .then((r) => r.json())
    .then(({ url }) => {
      window.location.href = url; // optionally open in popup instead
    });
}, []);

// After redirect back to your frontend:
useEffect(() => {
  fetch('http://localhost:3001/events', { credentials: 'include' })
    .then((r) => r.json())
    .then((data) => setEvents(data.events || []));
}, []);
```
