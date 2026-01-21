# Prompt Vault

A secure, single-page web application for storing, organizing, and retrieving AI prompts. Built with vanilla JavaScript and Supabase, Prompt Vault provides a lightweight yet powerful solution for managing your prompt collection with full authentication.

## Quick Overview

Prompt Vault is a modern prompt management tool that lets you:
- **Create** structured prompts with titles, descriptions, and full content
- **Organize** by category and tags for easy retrieval
- **Search** across all prompts with multiple filter options
- **Share** prompts via copy-to-clipboard functionality
- **Manage** your prompts with full CRUD operations (create, read, update, delete)
- **Secure** your data with Supabase authentication

## ✨ Features

### 🔐 Authentication
- **User Accounts** - Sign up with email and password
- **Secure Access** - Login required to access the app
- **Account Management** - View logged-in email and logout options
- **Email Signup** - Optional email confirmation (configurable in Supabase)
- **Session Persistence** - Stay logged in across browser sessions

### 📝 Prompt Management
- **Create Prompts** - Add new prompts with title, description, and body content
- **Edit Prompts** - Modify existing prompts anytime
- **Delete Prompts** - Remove prompts you no longer need
- **View Full Content** - Modal view for reading complete prompt text
- **Quick Copy** - Copy prompt content to clipboard with one click

### 🏗️ Organization
- **Categories** - Organize prompts into 5 categories: Work, Creative, Technical, Social Media, Other
- **Tags** - Add multiple tags to each prompt for flexible organization
- **Tag Filtering** - Click any tag to filter prompts matching that tag
- **Multi-tag Filtering** - Combine multiple tag filters to narrow results

### 🔍 Search & Filter
- **Text Search** - Find prompts by searching title, description, or content
- **Category Filter** - Show only prompts from a selected category
- **Prompt Sorting** - Sort by newest first, oldest first, A-Z, or Z-A (preference saved)
- **Tag Autocomplete** - Get smart suggestions as you type tags with keyboard navigation
- **Search Clear Button** - Quickly clear search with one click
- **Combined Filters** - Use search + category + tags together for precise results
- **Real-time Results** - See filtered results instantly as you type

### 🎨 Design & UX
- **Dark Theme** - Modern, eye-friendly dark interface
- **Responsive** - Works seamlessly on mobile, tablet, and desktop
- **Modal Interface** - Clean popups for adding, editing, and viewing prompts
- **Visual Feedback** - Hover effects, animations, and notifications
- **Accessibility** - Keyboard support and semantic HTML structure

### ⚡ Technical
- **No Build Process** - Just open `index.html` in your browser
- **Vanilla JavaScript** - No framework dependencies
- **Real-time Sync** - Changes sync across browser sessions instantly
- **XSS Protection** - All user input escaped to prevent injection attacks
- **CDN Hosted** - Supabase SDK loaded from CDN (no npm required)

## Getting Started

### Prerequisites

- **Browser** - Modern web browser (Chrome, Firefox, Safari, Edge)
- **Supabase Account** - Free tier at [supabase.com](https://supabase.com)
- **Text Editor** - To add your Supabase credentials

### Installation

#### 1. Create a Supabase Project

1. Go to [supabase.com](https://supabase.com) and sign up (free)
2. Create a new project
3. Wait for the project to initialize

#### 2. Create the Database Table

In Supabase, go to **SQL Editor** and run this query to set up your database:

```sql
-- Create prompts table
CREATE TABLE prompts (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  description TEXT,
  category TEXT NOT NULL,
  body TEXT NOT NULL,
  tags TEXT[],
  created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);

-- Create indexes for better query performance
CREATE INDEX idx_prompts_user_id ON prompts(user_id);
CREATE INDEX idx_prompts_category ON prompts(category);
CREATE INDEX idx_prompts_tags ON prompts USING GIN(tags);
CREATE INDEX idx_prompts_created_at ON prompts(created_at DESC);

-- Enable Row Level Security
ALTER TABLE prompts ENABLE ROW LEVEL SECURITY;

-- Create policy to restrict access to user's own prompts
CREATE POLICY "Users can only see their own prompts" ON prompts
  FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users can only insert their own prompts" ON prompts
  FOR INSERT
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can only update their own prompts" ON prompts
  FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can only delete their own prompts" ON prompts
  FOR DELETE
  USING (auth.uid() = user_id);

-- Create trigger for automatic updated_at timestamp
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = TIMEZONE('utc'::text, NOW());
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_prompts_updated_at BEFORE UPDATE ON prompts
  FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

#### 3. Get Your Credentials

1. In Supabase, go to **Settings** → **API**
2. Copy your **Project URL** (looks like `https://xxxxx.supabase.co`)
3. Copy your **anon/public** key (long string starting with `eyJ...`)

#### 4. Configure the App

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd promptvault
   ```

2. Open `index.html` with a text editor

3. Find the Supabase configuration section (around line 1097-1098):
   ```javascript
   const SUPABASE_URL = 'YOUR_SUPABASE_URL';
   const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
   ```

4. Replace with your actual credentials

#### 5. Open and Use

Simply open `index.html` in your web browser. You'll be prompted to create an account or log in.

```bash
# No server needed! Just open the file:
open index.html
# Or use your browser's File → Open menu
```

## Usage Walkthrough

### First Time: Sign Up

1. Open `index.html` in your browser
2. The login modal appears automatically
3. Click "Need an account? Sign up" to switch to signup mode
4. Enter your email and password (minimum 6 characters)
5. Click "Sign Up"
6. Check your email for confirmation link (if required by your Supabase settings)
7. Once confirmed, log in with your credentials

### Returning Users: Login

1. Open `index.html`
2. The login modal appears if you're not logged in
3. Enter your email and password
4. Click "Login"
5. You're now in the app and can see your prompts

### Adding a Prompt

1. Click **"+ Add Prompt"** button in the top right
2. Fill in the form:
   - **Title** (required) - What is this prompt for?
   - **Description** (optional) - Brief context or use case
   - **Category** (required) - Choose from Work, Creative, Technical, Social Media, Other
   - **Prompt** (required) - The full prompt text/content
   - **Tags** (optional) - Add relevant tags by typing and pressing Enter or clicking "Add"
3. Click **"Save Prompt"**
4. You'll see a confirmation and the new prompt appears in your list

### Searching & Filtering

**Text Search:**
- Type in the "Search prompts..." box (or press **Cmd/Ctrl+K** to jump to search)
- Searches across title, description, and prompt content
- Results update in real-time
- Click the **×** button to clear search instantly, or press **Escape** when focused

**Sorting:**
- Use the "Sort by" dropdown next to the category filter
- Choose: Newest first, Oldest first, A-Z, or Z-A
- Your preference is saved automatically in browser storage
- Sorting applies to all filtered results

**Category Filter:**
- Use the dropdown next to the search box
- Shows only prompts in the selected category
- Select "All Categories" to see everything

**Tag Filter:**
- Click any tag button below the controls to add a filter
- Tag becomes highlighted/active
- Shows only prompts with that tag
- **Click multiple tags** to show only prompts with ALL selected tags
- Click a highlighted tag again to deselect it

**Tag Autocomplete (When Adding Tags):**
- When adding tags to a prompt, type to see suggestions
- Arrow keys (↑↓) navigate suggestions
- Press **Enter** or **Tab** to select a suggestion
- Press **Escape** to close the dropdown
- Suggests tags from all your existing prompts

**Combining Filters:**
- Use search, category, and tags together
- The app shows only prompts matching ALL active filters
- Stats show total prompts and filtered count

### Viewing a Prompt

1. Click on any prompt card
2. A modal opens showing the full prompt content
3. You can:
   - **Copy** the prompt to clipboard (📋 button)
   - **Edit** the prompt (✏️ button)
   - Close the modal (× button or click background)

### Editing a Prompt

1. Click the **✏️ Edit button** on any prompt card or in the view modal
2. The edit form opens with your current content
3. Make changes to any field
4. Modify tags by:
   - Adding new tags (type and press Enter or click "Add")
   - Removing tags (click the × on each tag)
5. Click **"Save Prompt"** to save changes
6. The list updates with your changes

### Deleting a Prompt

1. Click the **🗑️ Delete button** on any prompt card
2. Confirm the deletion in the popup
3. The prompt is permanently removed

### Copying a Prompt

1. Click the **📋 Copy button** on any prompt card OR
2. View the full prompt and click "📋 Copy"
3. The prompt content is copied to your clipboard
4. A "Copied to clipboard" notification appears
5. Paste anywhere with Ctrl+V (or Cmd+V on Mac)

### Managing Your Account

**View Your Email:**
- Your logged-in email appears in the top right corner

**Logout:**
- Click the **"Logout"** button in the top right
- You'll be returned to the login screen
- Your data stays safe in Supabase

## Database Schema

The `prompts` table stores your prompt data:

| Field | Type | Notes |
|-------|------|-------|
| `id` | UUID | Unique identifier (auto-generated) |
| `user_id` | UUID | Links prompt to authenticated user |
| `title` | TEXT | Prompt title (required) |
| `description` | TEXT | Brief description (optional) |
| `category` | TEXT | One of: work, creative, technical, social, other |
| `body` | TEXT | Full prompt content (required) |
| `tags` | TEXT[] | Array of tag strings |
| `created_at` | TIMESTAMP | When prompt was created |
| `updated_at` | TIMESTAMP | When prompt was last modified |

**Key Points:**
- `user_id` ensures each user only sees their own prompts
- Indexes on `user_id`, `category`, `created_at`, and `tags` optimize query speed
- `updated_at` is automatically maintained via database trigger
- Prompts are deleted automatically if user account is deleted

## Architecture

### Single-File Application

The entire app is contained in `index.html` (no build process required):

```
promptvault/
├── index.html       # Complete application (HTML + CSS + JavaScript)
├── favicon.svg      # Browser tab icon
├── README.md        # This documentation
├── SETUP.md         # Detailed setup instructions (legacy)
├── CLAUDE.md        # Development guidelines
└── .git/            # Version control
```

### Component Structure (in index.html)

| Section | Lines | Purpose |
|---------|-------|---------|
| **Head & Meta** | 1-8 | Page title, viewport, Supabase SDK |
| **CSS Styles** | 9-948 | Complete styling with CSS custom properties |
| **HTML Structure** | 951-1094 | Page layout and modal definitions |
| **JavaScript** | 1095-1953 | App logic, auth, and database operations |

### Technology Stack

- **Frontend** - HTML5, CSS3, ES6+ JavaScript
- **Database** - Supabase (PostgreSQL)
- **Authentication** - Supabase Auth (email/password)
- **SDK** - Supabase JS Client (loaded from CDN)
- **Hosting** - Static file (GitHub Pages, Netlify, Vercel, etc.)

### Key JavaScript Objects & Variables

```javascript
// Configuration
SUPABASE_URL              // Supabase project URL
SUPABASE_ANON_KEY        // Supabase public API key

// State
supabase                  // Supabase client instance
currentUser              // Currently logged-in user object
prompts[]                // Array of all user's prompts
currentTags[]            // Tags being added to a new/edited prompt
activeTagFilters         // Set of currently selected tag filters
editingId                // ID of prompt being edited (null if creating)
authMode                 // 'login' or 'signup' mode
```

## Code Organization

### Authentication Flow

```
init() → checkAuth() → currentUser found?
  ├─ YES: loadPrompts() → renderPrompts()
  └─ NO: openAuthModal()

handleAuth(event) → signUp() or signInWithPassword()
  ├─ Success: closeAuthModal() → loadPrompts()
  └─ Error: Show error message

signOut() → Clear session → openAuthModal()
```

### Prompt Management Flow

```
loadPrompts() → Fetch all user prompts from Supabase
              → renderPrompts()
              → updateTagFilters()
              → updateStats()

renderPrompts() → Filter by search/category/tags
                → Generate HTML for each card
                → Attach click handlers

savePrompt() → Validate input
            → INSERT (new) or UPDATE (edit)
            → loadPrompts() to refresh
            → showNotification()

deletePrompt() → Confirm with user
               → DELETE from database
               → loadPrompts() to refresh
```

## Security Considerations

### ✅ Current Protections

- **XSS Prevention** - All user content escaped via `escapeHtml()` before rendering
- **Authentication Required** - Login required to access the app
- **User Data Isolation** - RLS policies ensure users only see/modify their own prompts
- **Password Security** - Minimum 6 characters, handled by Supabase Auth
- **HTTPS Only** - Supabase enforces HTTPS for all connections

### 🔐 Production Recommendations

1. **Keep RLS Policies Tight** - Review the SQL policies to ensure users can only access their own data
2. **Enable Email Verification** - Require email confirmation for new signups
3. **Use Strong Passwords** - Encourage users to use passwords longer than 6 characters
4. **Monitor Access** - Review Supabase audit logs for suspicious activity
5. **Regular Backups** - Set up automated backups of your Supabase database
6. **Rate Limiting** - Configure Supabase rate limiting to prevent abuse
7. **Update Dependencies** - Keep the Supabase JS SDK updated (it's loaded from CDN)

### ⚠️ Important Notes

- **Public API Key** - The Supabase `anon key` is public and visible in your HTML. The RLS policies protect your data.
- **Password Recovery** - Supabase provides built-in password reset via email (configured in their dashboard)
- **Data Ownership** - Users can only modify prompts they created (enforced by RLS)
- **Sharing Data** - The app doesn't have built-in sharing; you'd need to add features to share prompts

## Troubleshooting

### Login Issues

**"Invalid login credentials"**
- Check your email spelling
- Verify password is correct (case-sensitive)
- Make sure you've signed up for an account first

**"Check your email to confirm your account"**
- Supabase is requiring email verification
- Check your email (spam folder too) for confirmation link
- After confirming, you can log in

**Login modal won't close**
- Make sure you're successfully logged in (check for email in top right)
- Try refreshing the page

### Data Issues

**"Prompts not saving"**
- Check browser console for error messages (F12 → Console)
- Verify Supabase credentials are correct in `index.html`
- Ensure you're logged in
- Check that `user_id` field exists in your `prompts` table

**"Can't see old prompts"**
- The app now uses authentication - prompts are per-user
- You're only seeing prompts you created
- Old prompts from before auth was added won't be visible (they have no owner)
- Contact project maintainers if you need help migrating data

**"Some prompts disappeared"**
- Check that filters aren't too restrictive
- Clear all tag filters (click active tags to deselect)
- Try clearing search box
- Click "All Categories" to reset category filter

### Display Issues

**"Styling looks broken"**
- Clear browser cache (Ctrl+Shift+Delete or Cmd+Shift+Delete on Mac)
- Try a different browser
- Refresh the page (F5 or Cmd+R)
- Check that CSS custom properties are supported (all modern browsers support them)

**"Modal is hard to see"**
- Check monitor brightness/contrast
- Try zooming in (Ctrl+Plus or Cmd+Plus)
- Dark theme is intentional; there's no light mode toggle currently

**"Page is too small/large"**
- Use browser zoom (Ctrl+0 / Cmd+0 to reset)
- Mobile users: rotate device or pinch to zoom

### Performance Issues

**"App feels slow"**
- If you have many prompts (1000+), search might be slower
- This is normal - consider archiving old prompts
- Check your internet connection to Supabase

**"Changes not syncing"**
- Refresh the page (F5)
- Check that you're logged in
- Verify Supabase credentials are correct

## Project Status

### ✅ Completed

- Full authentication (login, signup, logout)
- Complete CRUD operations (create, read, update, delete)
- Multi-filter search system (text, category, tags)
- Prompt sorting with multiple options (newest, oldest, A-Z, Z-A)
- Sort preference persistence via localStorage
- Search clear button for quick clearing
- Tag autocomplete with keyboard navigation
- Tag management and filtering
- Category organization
- Modal-based UI for all operations
- Copy to clipboard functionality
- Responsive design (mobile, tablet, desktop)
- Dark theme with modern design
- XSS protection via HTML escaping
- Supabase database integration
- Row-level security for user data isolation
- Real-time data synchronization
- User account management
- Keyboard shortcuts (Cmd/Ctrl+K for search)

### 🔄 Future Enhancements

- Prompt favorites/starring system
- Usage statistics and tracking
- Import/export functionality (JSON, CSV)
- Prompt templates with variables
- Sharing capabilities (generate shareable links)
- Light/dark mode toggle
- Prompt versioning and history
- Collections/folders for grouping
- Advanced search (regex, fuzzy matching)
- Collaborative editing (team prompts)
- Bulk operations (delete multiple, apply tags)

## Development

### Code Conventions

- **Async/Await** - All database operations use async/await
- **Error Handling** - Wrapped in try/catch blocks
- **XSS Protection** - Always use `escapeHtml()` when rendering user content
- **CSS Variables** - Theming via `:root` CSS custom properties
- **State Management** - Module-level variables (prompts, currentTags, etc.)

### Making Changes

1. Edit `index.html` directly (no build process)
2. Test in your browser
3. Check browser console (F12) for any errors
4. Commit and push to your branch

### Adding New Features

The app is designed to be simple to modify. Key areas:

- **Add a field** - Update HTML form, add to savePrompt(), and create a database migration
- **Change styling** - Modify CSS variables in `:root` or specific component styles
- **Add a filter** - Duplicate existing filter logic in `renderPrompts()`
- **Add a button** - Create a new function and attach via `onclick`

## Browser Support

Works on all modern browsers:

| Browser | Version | Support |
|---------|---------|---------|
| Chrome/Chromium | Latest | ✅ Full support |
| Firefox | Latest | ✅ Full support |
| Safari | Latest | ✅ Full support |
| Edge | Latest | ✅ Full support |
| Mobile Chrome | Latest | ✅ Full support |
| Mobile Safari (iOS) | 12+ | ✅ Full support |
| Samsung Internet | Latest | ✅ Full support |

## Deployment

The app is a static HTML file and can be deployed anywhere:

### GitHub Pages

```bash
git push origin main
# Enable Pages in repo settings
# Your app is now live at: username.github.io/promptvault
```

### Netlify

1. Drag `index.html` to [netlify.com](https://netlify.com)
2. Your app is live in seconds

### Vercel

```bash
vercel --prod
```

### Traditional Hosting

Upload `index.html`, `favicon.svg`, and any other assets to any web server.

**All free options available** - just hosting a static file!

## Contributing

This is a personal project, but feel free to fork and customize for your needs. If you have improvements, feel free to submit a pull request.

## Support & Questions

For issues, feature requests, or questions:

1. Check the **Troubleshooting** section above
2. Review **CLAUDE.md** for development guidelines
3. Check browser console (F12 → Console) for error messages
4. Open an issue on GitHub

## License

MIT - Use freely for personal and commercial projects.

---

**Last Updated:** January 2026
**Version:** 2.2.0
**Status:** All Core Features Complete ✅

## Changelog

### v2.2.0 - UX Enhancements
- ✅ Added prompt sorting (newest, oldest, A-Z, Z-A)
- ✅ Sort preference persistence via localStorage
- ✅ Search clear button for quick clearing
- ✅ Tag autocomplete with dropdown suggestions
- ✅ Keyboard navigation for autocomplete (arrow keys, Enter, Tab, Escape)
- ✅ Keyboard shortcut for search (Cmd/Ctrl+K)
- ✅ Improved search UX with clear button visibility

### v2.1.0 - Enhancement Updates
- ✅ Improved tag filtering UI
- ✅ Enhanced tag management in prompt modal
- ✅ Better keyboard support throughout app

### v2.0.0 - Authentication Update
- ✅ Added Supabase Auth (email/password signup and login)
- ✅ User account management with email display
- ✅ Row-level security for user data isolation
- ✅ Login/logout functionality
- ✅ Automatic session persistence
- ✅ User-specific prompt storage
- ✅ Updated database schema with user_id field
- ✅ Improved security with RLS policies
- ✅ Updated authentication modal with signup option

### v1.0.0 - Initial Release
- Full CRUD operations
- Multi-filter search and tag system
- Responsive dark theme UI
- Supabase database integration
- XSS protection and security measures

---

Made with ❤️ for prompt enthusiasts everywhere
