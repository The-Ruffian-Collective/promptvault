# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Prompt Vault is a single-page web application for storing, organizing, and retrieving AI prompts. It uses vanilla JavaScript with Supabase as the backend database.

## Architecture

**Single-file application**: The entire app is contained in `index.html` with embedded CSS and JavaScript (no build process required).

**Tech stack**:
- Frontend: Vanilla HTML/CSS/JavaScript
- Database: Supabase (PostgreSQL)
- Authentication: Not implemented yet (RLS policy allows all access)

**Key components** (all in `index.html`):
- Supabase client configuration (lines 492-494)
- CRUD operations for prompts table
- Search/filter system (text, category, tags)
- Modal-based UI for adding/editing prompts
- Clipboard copy functionality

## Database Schema

The `prompts` table in Supabase:
```sql
CREATE TABLE prompts (
  id UUID PRIMARY KEY,
  title TEXT NOT NULL,
  description TEXT,
  category TEXT NOT NULL,
  body TEXT NOT NULL,
  tags TEXT[],  -- PostgreSQL array
  created_at TIMESTAMP,
  updated_at TIMESTAMP
)
```

Indexes exist on: `category`, `tags` (GIN), `created_at`

## Setup and Configuration

**Initial setup**: See `SETUP.md` for detailed Supabase setup instructions.

**Configuration**: Update Supabase credentials in `index.html` around line 492-494:
```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

**Running locally**: Simply open `index.html` in a browser. No build step or server required.

## Code Conventions

- CSS uses CSS custom properties for theming (see `:root` in styles)
- All database operations are async/await with try/catch error handling
- HTML is escaped via `escapeHtml()` utility to prevent XSS
- State is stored in module-level variables (`prompts`, `currentTags`, `editingId`, `activeTagFilters`)

## Important Notes

- **Security**: RLS policy is currently wide open (`USING (true)`). If adding authentication, update the policy in Supabase.
- **XSS Protection**: Always use `escapeHtml()` when rendering user-generated content
- **Tag handling**: Tags are stored as PostgreSQL arrays, not JSON. Use Supabase array operators for queries.
- **Fallback**: The app shows a warning notification if Supabase credentials aren't configured
