---
layout: post
title: "PathPilot Gets a Database: Authentication, Chat History, and Google Calendar Integration"
date: 2025-08-24 10:30:00
categories: [ai, coding, projects]
tags: [react, openai, gpt-4, cursor, supabase, authentication, oauth, google-calendar]
---

Two weeks ago, PathPilot was a proof of concept. Today... it still is but it's also has user accounts, persistent chat history, and Google Calendar integration. Leveraging Cursor to build it saved time for most things, but led to some interesting debugging challenges along the way.

### TL;DR

PathPilot now has user authentication, persistent chat history, and Google Calendar integration. Built with Supabase for the database and migrated from custom JWT to Supabase Auth. The UI got an upgrade with a proper chat history sidebar and calendar management features.

---

### What's New

PathPilot evolved from a simple chatbot into a comprehensive decision-making and productivity tool. Here's what changed:

* **User Accounts** – Register, login, and persistent sessions with Supabase Auth
* **Chat History** – Save, load, and manage your decision-making conversations
* **Google Calendar Integration** – OAuth2 authentication and full calendar management
* **Password Reset Flow** – Complete authentication recovery system

Each feature builds on the existing AI assistants, making PathPilot more useful for ongoing decision-making and productivity.

---

### A Few Things I'm Proud Of

#### 1. **Chat History with Supabase (PostgreSQL under the hood)**

Every chat session now gets saved to the database with proper user association:

```sql
-- Database schema
CREATE TABLE IF NOT EXISTS chats (
    id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    title VARCHAR(500) NOT NULL,
    messages JSONB NOT NULL,
    assistant_id VARCHAR(100) NOT NULL,
    thread_id VARCHAR(255),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

The chat history sidebar shows all your previous conversations, organized by assistant type and date. You can load any previous chat and continue where you left off.


This ensures users can only access their own data while maintaining a clean API.

#### 2. **Google Calendar OAuth2 Integration**

Adding Google Calendar integration was the most complex feature. The OAuth2 flow handles token storage, refresh, and calendar management:

```javascript
// OAuth2 callback handling
app.get('/api/auth/google/callback', async (req, res) => {
  const { code, state } = req.query;
  const userId = state; // User ID passed in state parameter
  
  try {
    const tokens = await calendarService.getTokensFromCode(code);
    
    // Store tokens in database
    const { error } = await supabaseAdmin
      .from('user_calendar_tokens')
      .upsert({
        user_id: userId,
        access_token: tokens.access_token,
        refresh_token: tokens.refresh_token
      });
    
    if (error) throw error;
    
    // Redirect to frontend with success
    res.send(`
      <html>
        <body>
          <script>
            window.location.href = '${process.env.FRONTEND_URL}/?connected=true&success=true';
          </script>
        </body>
      </html>
    `);
  } catch (error) {
    console.error('OAuth error:', error);
    res.redirect(`${process.env.FRONTEND_URL}/?error=auth_failed`);
  }
});
```

The calendar integration includes full CRUD operations for events, calendar listing, and recurring event support for habit tracking.


#### 3. **React Router Integration for Password Reset**

The password reset flow required proper routing and state management:

```javascript
// PasswordReset component
const PasswordReset = () => {
  const { supabase } = useAuth();
  const navigate = useNavigate();
  const [password, setPassword] = useState('');
  const [message, setMessage] = useState('');
  const [error, setError] = useState('');

  const handlePasswordReset = async (e) => {
    e.preventDefault();
    const { error } = await supabase.auth.updateUser({ password });
    
    if (error) {
      setError(error.message);
    } else {
      setMessage('Password successfully updated! Redirecting...');
      setTimeout(() => navigate('/'), 2000);
    }
  };
  
  // ... render form
};
```

### Stack Evolution

* **Frontend**: React + Material-UI + React Router
* **Backend**: Node.js + Express + Google APIs
* **Database**: Supabase (PostgreSQL) with RLS
* **Authentication**: Supabase Auth (migrated from custom JWT)
* **Calendar**: Google Calendar API with OAuth2
* **AI**: OpenAI's Assistants API

---

### Tools That Helped

#### **Cursor**: Saved a lot of time on building. What really amazed me was that it was context sensitive - I wanted to add a user accounts, and the AI 

#### **Supabase**: My first time using Supabase (I've been recommended it multiple times). I'm glad I tried it out - its easy, powerful, and free!

```sql
-- Row Level Security policies
CREATE POLICY "Users can view their own chats" ON chats
    FOR SELECT USING (auth.uid()::text = user_id::text);
```

The RLS policies ensure users can only access their own data, and the real-time subscriptions make future features much easier to implement.

---

### What I Learned

1. **Setting up user accounts doesn't have to be a headache** – Supabase makes it easy with built-in authentication and database management.

2. **Environment variables everywhere** – The number of API keys and secrets grew significantly. Proper environment management is crucial.

---

### Challenges Overcome

#### **OAuth Redirect Loops**
The initial OAuth implementation had redirect loops where users would complete authentication but get sent back to the OAuth page. This was solved by:
- Proper state parameter handling
- HTML response with JavaScript redirect instead of server-side redirect
- Frontend URL parameter detection for success states

---

### Want to Try It?

You can mess around with it [here](https://pathpilot-frontend.onrender.com/).
