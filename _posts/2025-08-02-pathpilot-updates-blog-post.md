---
layout: post
title: "PathPilot Gets Serious: Authentication and Chat History"
date: 2025-08-02 10:30:00
categories: [ai, coding, projects]
tags: [react, openai, gpt-4, cursor, supabase, authentication, jwt]
---

## PathPilot Gets a Database: Authentication and Chat History

Two weeks ago, PathPilot was a proof of concept. Today... it still is but it also has now has user accounts and persistent chat history. Leveraging Cursor to build it saved on time for most things, but led to bad engineering at others.

### TL;DR

PathPilot now has user authentication and chat history persistence. Built with Supabase for the database and JWT for auth. The UI got an  upgrade with a proper chat history sidebar.

---

### What's New

PathPilot evolved from a simple chatbot into a more robust decision-making tool. Here's what changed:

* **User Accounts** – Register, login, and persistent sessions
* **Chat History** – Save, load, and manage your decision-making conversations

Each feature builds on the existing AI assistants, making PathPilot more useful for ongoing decision-making.

---

### A Few Things I'm Proud Of

#### 1. **Supabase + JWT Authentication (No more anonymous chats)**

I finally bit the bullet and added proper user authentication. The setup uses Supabase for the database and JWT tokens for session management:

```javascript
// Inside server.js
const authenticateToken = (req, res, next) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];

  if (!token) {
    return res.status(401).json({ error: 'Access token required' });
  }

  jwt.verify(token, JWT_SECRET, (err, user) => {
    if (err) {
      return res.status(403).json({ error: 'Invalid token' });
    }
    req.user = user;
    next();
  });
};
```

The authentication flow is pretty lean: register/login → get JWT token → use token for all subsequent requests. No more losing your chat history when you refresh the page.

#### 2. **Chat History with Supabase (PostgreSQL under the hood)**

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


### Stack Evolution

* **Frontend**: React + Material-UI (upgraded from basic CSS)
* **Backend**: Node.js + Express (unchanged)
* **Database**: Supabase (PostgreSQL) - NEW
* **Authentication**: JWT + bcrypt - NEW
* **AI**: OpenAI's Assistants API (unchanged)

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


### Want to Try It?

You can mess around with it [here](https://pathpilot-frontend.onrender.com/).

---

*Two weeks ago, PathPilot was a weekend project. Today, it's a tool I actually use daily for decision-making. The difference? Persistence. Sometimes the best features aren't the flashy AI ones — they're the ones that make the tool actually usable in real life.* 