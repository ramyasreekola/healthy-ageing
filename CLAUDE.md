# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a static website for "Team Graceful Ageing" - an interactive survey application that helps users explore and select themes related to healthy aging. The site collects user preferences across six thematic areas and submits them via Formspree.

## Architecture

### Single-Page Application Structure

This is a vanilla HTML/CSS/JavaScript application with no build process or dependencies. The entire application runs in the browser with two main steps:

1. **Step 1 (Theme Selection)**: Users browse and select from six aging-related themes displayed in a card carousel
2. **Step 2 (User Information Form)**: Users provide personal details and submit their selections

### Key Components

**Theme Data Source**: `outlive_themes.json`
- Contains structured theme data (titles, images, descriptions, subthemes)
- Used as the single source of truth for theme content

**Inline JavaScript Modules** (located in `index.html`):
- **Modal System** (lines 302-412): Handles "More Details" popups showing theme subthemes
- **Theme Selection Logic** (lines 414-596): Manages checkbox state, card selection, navigation between steps, and form submission to Formspree

**Styling**: `styles.css`
- CSS custom properties (CSS variables) defined in `:root` for theming
- Responsive grid-based carousel using `grid-template-columns: repeat(auto-fit, minmax(280px, 1fr))`
- Mobile-first responsive design with breakpoints at 640px, 900px, and 1200px

### Data Flow

1. Theme data is hardcoded in JavaScript (lines 304-357) as a `THEMES` object
2. User selections are tracked via checkbox inputs with `.theme-checkbox` class
3. Selected themes are stored in `window.selectedThemes` array
4. On form submission, themes are injected into hidden input field `selectedThemesInput`
5. Form submits to Formspree endpoint: `https://formspree.io/f/mzzawbjd`

## Development Commands

### Running Locally

This is a static site with no build process. Simply open `index.html` in a browser:

```bash
open index.html
```

For better development experience with live reload, use a local server:

```bash
# Using Python 3
python3 -m http.server 8000

# Using Python 2
python -m SimpleHTTPServer 8000

# Using Node.js (if npx is available)
npx serve .
```

Then visit `http://localhost:8000`

### Deployment

The site automatically deploys to GitHub Pages via GitHub Actions (`.github/workflows/static.yml`) on every push to the `main` branch. No manual deployment steps required.

## Important Technical Details

### Theme Data Duplication

Currently, theme data exists in TWO places:
1. `outlive_themes.json` - appears to be unused
2. Inline JavaScript object `THEMES` (index.html:304-357)

When modifying themes, you must update the JavaScript object in `index.html`. The JSON file is currently not loaded by the application.

### Form Integration

- Form uses Formspree service (no backend required)
- Form endpoint: `https://formspree.io/f/mzzawbjd` (index.html:254)
- Hidden field `selectedThemesInput` (index.html:282) carries selected theme names
- Honeypot field `_gotcha` (index.html:285) provides basic spam protection

### State Management

- No framework or state management library used
- State stored in:
  - DOM (checkbox checked state)
  - CSS classes (`.selected` for visual feedback)
  - Global variable (`window.selectedThemes`)

### Accessibility Features

- ARIA labels on interactive elements
- Modal with proper `role="dialog"` and `aria-modal="true"`
- Keyboard support (Escape to close modal)
- Focus management (close button receives focus on modal open)

## Common Development Tasks

### Adding a New Theme

1. Add theme data to the `THEMES` object in index.html (around line 304)
2. Add corresponding HTML card in the carousel (around line 62)
3. Ensure the `data-theme` attribute on the "More details" button matches the THEMES key exactly

### Modifying Form Fields

Form structure is in index.html (lines 254-291). Fields currently collected:
- Full Name (required)
- Email (required)
- Phone Number (required)
- Age (required, 18-120)
- Additional Comments (optional)

All modifications require updating the HTML form structure only.

### Styling Changes

All styles are in `styles.css`. Key CSS custom properties (variables) in `:root`:
- `--accent`: Primary action color (currently yellow #f3e27a)
- `--accent-text`: Text on accent backgrounds
- `--text-primary`: Main text color
- `--text-muted`: Secondary text color
- `--radius-card`: Border radius for cards (18px)

### Testing Form Submissions

To test without sending real data to Formspree:
1. Temporarily change form action to a test endpoint
2. Or use browser DevTools to inspect form data before submission
3. Consider adding `target="_blank"` to form tag during testing to preserve state

## File Structure

```
healthy-ageing/
├── index.html              # Main application (includes all JavaScript)
├── styles.css              # All styles and responsive design
├── outlive_themes.json     # Unused theme data (for reference)
├── styles-backup.css       # Backup of previous styles
├── README.md               # Project description
└── .github/
    └── workflows/
        └── static.yml      # GitHub Pages deployment config
```
