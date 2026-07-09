# Friends Calendar - Technical Evaluation Report

This report provides a detailed technical evaluation of the `AnnaKupyniak/friends-calendar` repository. The project is a full-stack real-time collaborative calendar and messaging web application allowing users to connect, chat, organize events, and log shared memories.

---

## 1. Project Structure Overview

The repository is structured as a monorepo consisting of two primary directories: `backend/` and `frontend/`. 

```text
friends-calendar/
├── backend/                  # Node.js, Express, MongoDB & Socket.IO
│   ├── config/               # Database and environment configurations
│   ├── controllers/          # Business logic for routes (auth, events, groups, memories, messages, users)
│   ├── middleware/           # Security, validation, auth protection, and file upload handlers
│   ├── models/               # Mongoose database models (User, Group, Event, Memory, Message, Friendship)
│   ├── routes/               # Express API routing tables
│   ├── migrateFriendships.js # Script for database schema migrations
│   └── server.js             # Express API entry point and Socket.IO event handler
│
└── frontend/                 # Vite + React Client
    ├── public/               # Public assets
    ├── src/
    │   ├── api/              # Axios instance configuration and global response interceptors
    │   ├── assets/           # React SVG/image assets
    │   ├── components/       # Reusable layout and base components (Calendar, Button, Header, Modal)
    │   ├── context/          # React Contexts for global state (Auth, Events, Friends, Memories)
    │   ├── features/         # Feature components (categories, events, friends, memories)
    │   ├── pages/            # View pages (Chat, Comments, Home, Login, Register, Profile, MyCalendar)
    │   ├── index.css         # Global stylesheets
    │   └── main.jsx          # Client entry point
    ├── eslint.config.js      # ESLint rules
    └── vite.config.js        # Vite compilation and proxy server configuration
```

---

## 2. Core Technologies

- **Frontend:**
  - **React (v19.2.0):** State rendering engine utilizing contexts and hooks.
  - **Vite (v7.2.4):** Development server and production bundler.
  - **Material-UI (MUI v7.3.6):** Component framework used for calendar pickers and material styles.
  - **Socket.IO Client (v4.8.3):** Real-time event communication with the backend.
  - **Axios (v1.13.5) / Native Fetch:** REST API communication clients.
- **Backend:**
  - **Node.js & Express (v5.2.1):** Serves the REST API. Utilizes Express 5, which handles promise rejections inside async route handlers natively without requiring external async-handler wrappers.
  - **MongoDB & Mongoose (v9.1.2):** Object Data Modeling (ODM) for database management.
  - **Socket.IO (v4.8.3):** Manages websocket connections, rooms, and broadcasts.
  - **Multer (v2.1.0) & Bcrypt (v6.0.0):** Middleware for handling multipart uploads and password hashing.

---

## 3. Strengths

- **Express 5 Adoption:** The server runs on Express 5. This allows the backend to handle asynchronous route handlers safely. If any mongoose query or database action fails, the rejected promise is caught and passed down to `errorHandler` automatically without needing try-catch wrappers or `express-async-handler`.
- **Dynamic Poly-Refs (`refPath`):** The Mongoose models utilize Mongoose's polymorphic reference configuration `refPath` for models like `Event` and `Memory`. This elegantly links calendar events and shared memories to either a `Friendship` or a `Group` dynamically.
- **Text & Tag Indexing:** The `User` model sets text indexes on `username` and `fullName`, while the `Memory` model indexes `tags` and text fields. This ensures search operations (like finding friends or filtering memories) are fast and scalable.
- **Vite Proxy Configuration:** The Vite dev server proxies `/api`, `/uploads`, and `/socket.io` requests to the Node server on port 5000. This avoids CORS issues during local development and allows seamless cookies sharing.
- **Decoupled Client State:** The React architecture encapsulates API calls and entity states inside clean, decoupled Context providers (`AuthContext`, `FriendsContext`, `EventsContext`), keeping layout components focused purely on presentation.

---

## 4. Potential Issues

### 4.1. Critical Security Vulnerabilities
- **WebSocket Impersonation & Hijacking:** 
  The Socket.IO implementation in [server.js](file:///C:/teamvoy/ai-repo-analyzer/backend/server.js) contains no authentication check. In the `join-chat` and `send-message` handlers, the server trusts `userId`, `senderId`, and `senderInfo` fields provided directly by the client payload. Any connected websocket client can spoof another user's ID, join their rooms, and send messages on their behalf.
- **BOLA/IDOR on Group Messages:**
  In `getMessages` inside [messageController.js](file:///C:/teamvoy/ai-repo-analyzer/backend/controllers/messageController.js), if a `groupId` is supplied in the query string, the controller fetches and returns all messages without verifying if the authenticated user (`req.user`) is actually a member of that group.
- **BOLA/IDOR on Calendar Events:**
  In `createEvent` inside [eventController.js](file:///C:/teamvoy/ai-repo-analyzer/backend/controllers/eventController.js), the backend creates a calendar event for a friendship or group ID directly from the request body without checking if the creator belongs to that entity.

### 4.2. Functional & Integration Bugs
- **Broken Group Add Member Feature:**
  In [FriendsContext.jsx](file:///C:/teamvoy/ai-repo-analyzer/frontend/src/context/FriendsContext.jsx), `addMembersToGroup` posts `{ userIds }` to the backend. However, `addMembers` in [groupController.js](file:///C:/teamvoy/ai-repo-analyzer/backend/controllers/groupController.js) extracts `members` (`const { members } = req.body;`). Because of this mismatch, `members` evaluates to `undefined`, making the backend push `undefined` into the group's members array. Mongoose's schema validation throws a `CastError` when saving, rendering the "Add Member" functionality completely broken.
- **Group Categories Schema & Logic Mismatch:**
  In [Group.js](file:///C:/teamvoy/ai-repo-analyzer/backend/models/Group.js), categories are defined as an array of subdocuments: `categories: [ { type: [String], trim: true } ]`. However, the group controller attempts to verify membership via `group.categories.includes(category)` (checking if an array of objects contains a string), which will always return `false`. This leads to duplicate category insertions and corrupted documents.
- **Group Validation Bypass:**
  In [validation.js](file:///C:/teamvoy/ai-repo-analyzer/backend/middleware/validation.js), `validateGroupInput` checks `title` instead of the schema-defined `name`. Since the frontend passes `name`, the group name format validation is bypassed entirely on group creations and updates.
- **Missing Password Reset Fields in User Schema:**
  The `getResetPasswordToken` method in [User.js](file:///C:/teamvoy/ai-repo-analyzer/backend/models/User.js) updates `resetPasswordToken` and `resetPasswordExpire` fields. Since these fields are not defined in the schema, Mongoose's default `strict: true` setting will strip them out before saving them, rendering password resets non-functional.

### 4.3. Code Cleanliness & Packaging Issues
- **Frontend Axios Client Inconsistency:**
  Multiple files on the frontend bypass the configured `apiClient` (which intercepts 401/403 errors and auto-clears user sessions) in favor of calling `axios` directly or using `fetch()` with raw configurations (e.g. `FriendsContext.jsx` and `Chat.jsx`). This prevents global session expirations from triggering a redirect to the login page.
- **Dead Client Dependency:**
  `multer` (a Node.js-only multipart form data parser) is listed in the dependencies of the frontend's `package.json`. This dependency is unused and cannot be compiled into client bundle files.

---

## 5. Recommendations for Improvement

1. **Fix Payload Mismatch in Group Add Member:**
   Standardize the payload between the frontend and backend. Either modify the frontend to send `members` or edit [groupController.js](file:///C:/teamvoy/ai-repo-analyzer/backend/controllers/groupController.js#L182):
   ```javascript
   // Change from:
   const { members } = req.body;
   // To:
   const { userIds } = req.body;
   const newMembers = Array.isArray(userIds) ? userIds : [userIds];
   ```
2. **Implement Socket.IO Middleware Authentication:**
   Authenticate Socket.IO clients during connection by extracting and verifying the JWT token. Bind `userId` directly to the `socket` object:
   ```javascript
   io.use((socket, next) => {
     const cookieHeader = socket.handshake.headers.cookie;
     // parse cookie, verify JWT, set socket.userId
     next();
   });
   ```
   Modify `send-message` and `join-chat` events to use `socket.userId` instead of trusting user IDs sent in the client payload.
3. **Enforce Authorization Guards in Message & Event APIs:**
   - In `getMessages`, verify that the logged-in user is a member of the group before sending back messages.
   - In `createEvent`, verify that the logged-in user is a member of the group or friendship before saving the event.
4. **Fix Group Categories Schema:**
   Change the schema in [Group.js](file:///C:/teamvoy/ai-repo-analyzer/backend/models/Group.js) to:
   ```javascript
   categories: {
     type: [String],
     default: []
   }
   ```
5. **Align validation.js with Group Schema:**
   Update `validateGroupInput` in [validation.js](file:///C:/teamvoy/ai-repo-analyzer/backend/middleware/validation.js) to validate `name` instead of `title`:
   ```javascript
   const { name, description, category, memberId, groupId } = req.body || req.params || req.query;
   if (name && (typeof name !== 'string' || name.trim().length < 2)) ...
   ```
6. **Extend UserSchema for Reset Tokens:**
   Define `resetPasswordToken: String` and `resetPasswordExpire: Date` in [User.js](file:///C:/teamvoy/ai-repo-analyzer/backend/models/User.js).
7. **Clean up Frontend API Calls:**
   Refactor all HTTP calls in `FriendsContext.jsx` and `Chat.jsx` to route through `apiClient` rather than importing `axios` directly or using `fetch`, ensuring proper error interception and token invalidation redirects.
8. **Prune Frontend Dependencies:**
   Remove `multer` from the frontend's `package.json`.
