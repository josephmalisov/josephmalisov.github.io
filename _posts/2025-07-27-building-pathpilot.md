---
layout: post
title: "Building PathPilot with Cursor, ChatGPT, and Some Coffee"
date: 2025-07-27 10:30:00
categories: [ai, coding, projects]
tags: [react, openai, gpt-4, cursor, pdf, supabase]
---

## Building PathPilot with Cursor, ChatGPT, and Some Coffee

I built [PathPilot](https://pathpilot-frontend.onrender.com/) because I was tired of chatbots that just answer questions and leave you hanging. I wanted a tool that actually helps you think — like that friend you go to when you need help making a decision.

### TL;DR

PathPilot is a decision-making assistant that generates structured plans using GPT-4. Built with React + Node.js, it has multiple AI “modes,” PDF export, and some clean UI touches. Cursor and ChatGPT sped up dev big time.

---

### What It Does

PathPilot isn’t just a chatbot — it’s a tool for turning vague thoughts into solid plans. You pick your assistant:

- `Path Planner` – general decisions and goal planning
- `Atomic Habits` – breaking/making habits
- `Essentialist` – prioritizing and saying no
- `Flow Zone` – deep work and focus

Each assistant runs on the OpenAI Assistants API under the hood. You enter a scenario, and it returns a structured response *and* a downloadable PDF.

---

### A Few Things I’m Proud Of

#### 1. **OpenAI Assistants (no streaming, just smart responses)**

I’m using the Assistants API rather than raw chat completions. That means each mode has its own system behavior baked in. Here’s the core of how a response works:

```ts
const thread = await openai.beta.threads.create();
const run = await openai.beta.threads.runs.create(thread.id, {
  assistant_id: assistantId,
  instructions: userInput,
});
```

Then I poll for completion and fetch the results from the thread. Much cleaner than prompt juggling.

#### 2. **PDF Export (HTML or custom layout)**

I wrote two strategies for generating PDFs:

- `html2pdf.js` – turns styled hidden HTML into a shareable PDF
- `jsPDF` – for precise layout, markdown support, and font styling

```ts
// Inside StickyPathPlanBanner.jsx
const handleExport = () => {
  generatePDF(latestPathPlan.content); // auto-selects rendering method
};
```

#### 3. **Sticky Plan Banner (React + MUI)**

After generating a plan, a sticky footer shows up with an export button. It uses `Fade`, `Paper`, and some celebratory UI state:

```tsx
<Fade in={true}>
  <Paper elevation={3} sx={{ position: 'fixed', bottom: 0, width: '100%' }}>
    <Button onClick={handleExport}>Export PathPlan</Button>
  </Paper>
</Fade>
```

4. Up and Running in one weekend. Does it have everything? No. But it's a proof of concept.

---

### Stack

- **Frontend**: React + Material UI (dark mode default, obviously)
- **Backend**: Node.js + Express
- **Auth & DB**: Supabase
- **AI**: OpenAI’s Assistants API

---

### Tools That Helped

#### **Cursor**: Live chat with the codebase + Git integration. It saved me hours.

#### **ChatGPT**: It helped me:

- Brainstorm
- Talk through design decisions
- Draft the assistant prompts / test them (I made bots to generate example prompts for my bots)
- Set up external tools (like Render and OpenAI Integrations)

I would note that it was important to consider when to use which tool. Cursor was great for some things, like debugging without having to copy/paste into ChatGPT. ChatGPT was better for giving advice on integrations.

---

### Want to Try It?

You can mess around with it [here](https://pathpilot-frontend.onrender.com/). There’s no login wall yet — just pick an assistant and start typing.
