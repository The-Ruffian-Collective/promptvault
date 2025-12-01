# Prompt Library - Setup Guide

## Quick Start

### 1. Set up Supabase

1. Go to [supabase.com](https://supabase.com) and create a free account
2. Create a new project
3. Go to **Table Editor** and create a new table called `prompts`

### 2. Create the Database Table

Run this SQL in the Supabase SQL Editor (Database → SQL Editor → New Query):

```sql
-- Create prompts table
CREATE TABLE prompts (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  title TEXT NOT NULL,
  description TEXT,
  category TEXT NOT NULL,
  body TEXT NOT NULL,
  tags TEXT[],
  created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);

-- Create index for faster searches
CREATE INDEX idx_prompts_category ON prompts(category);
CREATE INDEX idx_prompts_tags ON prompts USING GIN(tags);
CREATE INDEX idx_prompts_created_at ON prompts(created_at DESC);

-- Enable Row Level Security (RLS)
ALTER TABLE prompts ENABLE ROW LEVEL SECURITY;

-- Create policy to allow all operations (adjust later for auth)
CREATE POLICY "Enable all access for now" ON prompts
  FOR ALL
  USING (true)
  WITH CHECK (true);

-- Create updated_at trigger
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = TIMEZONE('utc'::text, NOW());
    RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER update_prompts_updated_at BEFORE UPDATE ON prompts
  FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

### 3. Get Your Credentials

1. In Supabase, go to **Settings** → **API**
2. Copy your **Project URL** (looks like `https://xxxxx.supabase.co`)
3. Copy your **anon/public** key (long string starting with `eyJ...`)

### 4. Configure the App

Open `prompt-library.html` and replace these lines (around line 484):

```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';  // Replace with your Project URL
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';  // Replace with your anon key
```

### 5. Run It

Just open the HTML file in your browser - no build process needed!

---

## Features

- ✅ **Full CRUD** - Create, read, update, delete prompts
- 🔍 **Multi-filter search** - Search by text, category, and tags
- 🏷️ **Tag system** - Multiple tags per prompt with filtering
- 📋 **One-click copy** - Copy prompts to clipboard instantly
- 💾 **Persistent storage** - All data saved to Supabase PostgreSQL
- 🎨 **Minimal modern UI** - Dark theme, clean design
- ⚡ **Real-time updates** - Changes sync immediately

---

## Database Schema

```
prompts
├── id (UUID, primary key)
├── title (TEXT, required)
├── description (TEXT, optional)
├── category (TEXT, required)
├── body (TEXT, required)
├── tags (TEXT[], array)
├── created_at (TIMESTAMP)
└── updated_at (TIMESTAMP)
```

---

## Categories

Default categories (can be customized in the HTML):
- Work
- Creative
- Technical
- Social Media
- Other

---

## Future Enhancements (for Claude Code)

Some ideas for iteration:

1. **Authentication** - Add Supabase Auth for multi-user
2. **Favorites** - Star/favorite important prompts
3. **Usage tracking** - Count how many times each prompt is used
4. **Export/Import** - JSON export for backup
5. **Template variables** - Support `{{variable}}` placeholders
6. **Sharing** - Generate shareable links
7. **Dark/Light mode toggle**
8. **Keyboard shortcuts** - Quick actions
9. **Prompt versioning** - Track changes over time
10. **Collections** - Group related prompts

---

## Notes

- The app uses localStorage as fallback if Supabase isn't configured
- RLS policy is wide open for now - tighten when adding auth
- All indexes are optimized for common queries
- Tags are stored as PostgreSQL arrays for efficient querying

---

## Deploy Options

1. **GitHub Pages** - Just commit and enable Pages
2. **Netlify** - Drag and drop the HTML file
3. **Vercel** - One-click deploy
4. **Cloudflare Pages** - Fast global CDN

All options are free for static HTML!
