# Prompt Vault

A structured way to collect, manage, and apply your best prompts.

Prompt Vault is a modern, single-page web application for storing, organizing, and retrieving AI prompts. Built with vanilla JavaScript and Supabase, it provides a lightweight yet powerful solution for managing your prompt collection.

## Features

✨ **Core Functionality**
- **Create & Store** - Add new prompts with titles, descriptions, and full prompt content
- **Organize** - Categorize prompts (Work, Creative, Technical, Social Media, Other)
- **Tag System** - Add multiple tags to prompts for flexible organization
- **Search & Filter** - Find prompts by text search or filter by category and tags
- **Quick Copy** - Copy prompt content to clipboard with a single click
- **View Full Prompt** - Modal view for reading complete prompt text
- **Edit & Delete** - Modify or remove prompts as needed

🎨 **Design**
- Dark theme with modern, clean UI
- Responsive design that works on mobile, tablet, and desktop
- Smooth animations and transitions
- Intuitive modal-based interface

⚡ **Technical**
- No build process required - just open `index.html` in your browser
- Vanilla JavaScript (no frameworks)
- Client-side with Supabase backend
- Real-time data sync across sessions
- XSS protection via HTML escaping

## Getting Started

### Prerequisites
- Modern web browser
- Supabase account (free tier works fine)

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd promptvault
   ```

2. **Set up Supabase**
   - Create a new project at [supabase.com](https://supabase.com)
   - Create a `prompts` table with the schema below
   - Copy your Supabase URL and anon key

3. **Configure credentials**
   - Open `index.html`
   - Find lines 782-783 (Supabase configuration section)
   - Replace `SUPABASE_URL` and `SUPABASE_ANON_KEY` with your credentials

4. **Run locally**
   - Open `index.html` in your web browser
   - Start adding and organizing prompts!

## Database Schema

The `prompts` table in Supabase uses the following schema:

```sql
CREATE TABLE prompts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title TEXT NOT NULL,
  description TEXT,
  category TEXT NOT NULL,
  body TEXT NOT NULL,
  tags TEXT[],
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Recommended indexes for performance
CREATE INDEX idx_prompts_category ON prompts(category);
CREATE INDEX idx_prompts_created_at ON prompts(created_at DESC);
CREATE INDEX idx_prompts_tags ON prompts USING GIN(tags);
```

### Field Descriptions
- `id` - Unique identifier (UUID)
- `title` - Prompt name/title (required)
- `description` - Brief description of the prompt
- `category` - One of: work, creative, technical, social, other
- `body` - The full prompt text (required)
- `tags` - PostgreSQL array of string tags
- `created_at` - Timestamp of creation
- `updated_at` - Timestamp of last update

## Usage

### Adding a Prompt
1. Click the **"+ Add Prompt"** button
2. Fill in the title (required) and optional description
3. Select a category
4. Paste or type your prompt content
5. Add tags by typing and clicking "Add"
6. Click "Save Prompt"

### Searching & Filtering
- **Text Search** - Type in the search box to find prompts by title, description, or content
- **Category Filter** - Use the dropdown to show prompts from a specific category
- **Tag Filter** - Click tag buttons to filter by one or more tags

### Managing Prompts
- **View** - Click a prompt card to see the full content in a modal
- **Copy** - Click the 📋 button to copy prompt content to clipboard
- **Edit** - Click the ✏️ button to modify a prompt
- **Delete** - Click the 🗑️ button to remove a prompt

## Project Status

### ✅ Completed
- Full CRUD operations (Create, Read, Update, Delete)
- Search and filtering system
- Category and tag management
- Modal UI for adding/editing/viewing
- Responsive design
- Supabase integration
- Dark theme with modern design system

### 🔄 In Progress / Planned
- User authentication
- Prompt sharing and export
- Advanced search/sorting options
- Import/export functionality
- Cloud sync for multiple devices
- Collections/folders for grouping prompts

## Architecture

### File Structure
```
promptvault/
├── index.html      # Complete single-file application
├── favicon.svg     # Browser tab icon
├── README.md       # This file
├── CLAUDE.md       # Development guidelines
└── SETUP.md        # Detailed setup instructions
```

### Key Components (in index.html)
- **HTML** (lines 693-778) - Page structure and modals
- **CSS** (lines 8-691) - Styling with CSS custom properties
- **JavaScript** (lines 780-1113) - App logic and Supabase integration

### Tech Stack
- **Frontend** - HTML5, CSS3, Vanilla JavaScript (ES6+)
- **Database** - Supabase (PostgreSQL)
- **Authentication** - Not yet implemented (RLS policy allows all access)
- **Deployment** - Static hosting (any CDN or web server)

## Security

### Current State
- RLS policy is open (`USING (true)`) - anyone with the anon key can access data
- All user input is escaped via `escapeHtml()` to prevent XSS
- No user authentication implemented yet

### Recommendations for Production
- Implement Supabase authentication (email, OAuth, etc.)
- Update RLS policies to restrict access to authenticated users
- Consider rate limiting on Supabase
- Regularly backup your prompts table
- Use HTTPS for all connections

## Browser Support

Works on all modern browsers:
- Chrome/Edge (latest)
- Firefox (latest)
- Safari (latest)
- Mobile browsers (iOS Safari, Chrome Mobile)

## Development

### Code Conventions
- Use async/await for database operations
- Wrap DB calls in try/catch error handlers
- Always use `escapeHtml()` when rendering user content
- CSS uses custom properties for theming (see `:root`)
- Module-level variables: `prompts`, `currentTags`, `editingId`, `activeTagFilters`

### Making Changes
1. Edit `index.html` directly
2. No build process or dependencies to install
3. Test in your browser
4. Commit and push to your branch

## Troubleshooting

### "Configure Supabase credentials first"
- Check that `SUPABASE_URL` and `SUPABASE_ANON_KEY` are set correctly in index.html
- Ensure they're not set to the placeholder values

### Prompts not saving
- Check browser console for error messages
- Verify Supabase credentials are correct
- Ensure your `prompts` table exists in Supabase
- Check that RLS policies allow insert/update operations

### Styling looks off
- Clear browser cache (Ctrl+Shift+Delete)
- Try a different browser
- Check for CSS custom property support (all modern browsers support it)

## Contributing

This is a personal project, but feel free to fork and customize for your needs!

## License

MIT - Use freely for personal and commercial projects.

## Support & Feedback

For issues, questions, or feature requests, please check the CLAUDE.md file for development guidelines or contact the project maintainers.

---

**Last Updated:** December 2024
**Version:** 1.0.0
