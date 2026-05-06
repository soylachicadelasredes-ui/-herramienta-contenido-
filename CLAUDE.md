# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

**Estratega de Contenido** is a single-page chat app that generates social media content plans for small businesses and entrepreneurs (Argentine Spanish). It uses a vanilla HTML/JS frontend and a Vercel serverless function backend that calls the Anthropic API.

## Architecture

The app has two parts:

- **`index.html`** — the entire frontend: HTML structure, all CSS, and all JavaScript in one file. No build step, no framework. The JS maintains a `history` array of `{role, content}` objects sent to the API on each turn.
- **`api/chat.js`** — Vercel serverless function (CommonJS). Receives `{ messages }` in the POST body, calls `claude-sonnet-4-6` with a hardcoded system prompt, and returns `{ content }`. The system prompt defines a rigid 7-section response structure for new niches, and free-form replies for follow-up questions.

## Deployment

Deployed on Vercel. `vercel.json` routes all `/api/*` requests to the serverless functions and everything else to `index.html`.

Required environment variable: `ANTHROPIC_API_KEY`

## Local Development

There is no build step. To run locally with the serverless function:

```bash
npm install
npx vercel dev
```

This requires the Vercel CLI and a `.env` file (or environment variable) with `ANTHROPIC_API_KEY`.

To test only the frontend (no API calls), open `index.html` directly in a browser — the send button will fail at the fetch but all UI logic is testable.

## Key Design Decisions

- The system prompt in `api/chat.js` is the core "product" — it controls tone (Argentine Spanish, no jargon), audience (time-strapped entrepreneurs), and enforces the 7-section structured plan format. Changes here directly affect output quality.
- The frontend uses `marked.js` (CDN) to render assistant responses as Markdown. User messages are set as plain text (`textContent`) to avoid XSS.
- Conversation history is kept in memory only (`let history = []`). Refreshing the page resets it.
- The model is pinned to `claude-sonnet-4-6` with `max_tokens: 4000`.
