# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**neosale-lp** is a **legacy static HTML landing page** served via Nginx. It's a simple, self-contained project with no build process, package manager dependencies, or dynamic backend requirements. This is a minimal, production-ready landing page maintained for backwards compatibility.

**Stack**: HTML5 + CSS + Vanilla JavaScript | **Server**: Nginx | **Status**: Maintenance mode

### Project Structure
```
neosale-lp/
├── index.html           # Main landing page (dark theme, primary color #6366f1)
├── index-white.html     # Alternative white theme variant
├── nginx.conf           # Nginx configuration (gzip, caching, security headers)
├── Dockerfile           # Multi-stage Docker build for Nginx
├── neosale-icon.png       # Logo/icon asset (36x36 minimum)
└── CLAUDE.md            # This file
```

**Note**: All CSS and JavaScript are embedded inline in the HTML files—there are no separate css/ or js/ directories.

## Quick Start

### Local Development

No build process required. Serve the files with any HTTP server:

**Python 3:**
```bash
python -m http.server 8000
# Access at http://localhost:8000
```

**Node.js (http-server package):**
```bash
npx http-server
# Access at http://localhost:8080
```

**macOS (SimpleHTTPServer):**
```bash
python -m SimpleHTTPServer 8000
```

Just open your browser to the local server URL and edit the HTML/CSS/JavaScript files directly—changes appear on refresh.

### Choosing Between index.html and index-white.html

- **index.html** (925 lines): Dark theme with indigo primary color (#6366f1), recommended for production
- **index-white.html** (890 lines): Light/white theme variant, for testing different visual styles
- Both files have identical structure, only CSS variables differ
- Nginx default serves `index.html` via the `index index.html` directive

## Key Features

### Embedded Styling & Scripts
- All CSS is in `<style>` tags within the HTML `<head>`
- All JavaScript is in `<script>` tags at the end of `<body>`
- Custom CSS variables for theming (--primary, --bg-dark, --text-light, etc.)
- Responsive design using modern CSS (flexbox, grid, media queries)
- Dark theme with color scheme: primary #6366f1 (indigo)

### Nginx Configuration (nginx.conf)
- **Gzip compression** for text, CSS, JavaScript, and SVG
- **Cache control**: 30-day expiry on static assets (images, fonts, CSS, JS)
- **Fallback routing**: `try_files $uri $uri/ /index.html` for SPA-like behavior
- **Security headers**:
  - `X-Frame-Options: SAMEORIGIN`
  - `X-Content-Type-Options: nosniff`
  - `X-XSS-Protection: 1; mode=block`

### Docker Deployment
The `Dockerfile` uses **nginx:alpine** as the base image:
1. Copies custom nginx.conf
2. Copies all HTML, CSS, JavaScript, and assets to `/usr/share/nginx/html`
3. Removes Dockerfile and nginx.conf from served files (security)
4. Exposes port 80
5. Runs nginx in foreground mode

## Common Development Tasks

### Editing Content & Styles
1. Edit `index.html` or `index-white.html` directly in your editor
2. Use your HTTP server to see changes on page refresh
3. All changes are immediate—no build step needed

**Editing inline CSS**: Located in the `<style>` block in `<head>`
**Editing inline JavaScript**: Located in the `<script>` block at the end of `<body>`

### Updating Colors & Theme

CSS variables are defined in the `:root` selector at the top of the `<style>` block. Modify these to change the entire color scheme:

```css
:root {
    --primary: #6366f1;              /* Main brand color */
    --primary-dark: #4f46e5;         /* Darker shade for hover/active states */
    --primary-light: #818cf8;        /* Lighter shade for backgrounds */
    --bg-dark: #0f0f23;              /* Main background */
    --bg-card: #1a1a2e;              /* Card backgrounds */
    --bg-input: #16162a;             /* Input field backgrounds */
    --text-light: #f1f5f9;           /* Primary text color */
    --text-gray: #94a3b8;            /* Secondary text */
    --text-muted: #64748b;           /* Muted text */
    --border-color: #2d2d4a;         /* Border color */
    --success: #22c55e;              /* Success state color */
    --error: #ef4444;                /* Error state color */
}
```

### Adding Assets
- Place images, fonts, or other static files in the root directory or create subdirectories (css/, js/, assets/)
- Update `nginx.conf` cache rules if adding new file types beyond jpg, jpeg, png, gif, ico, css, js, svg, woff, woff2:
  ```nginx
  location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|webp)$ {
      expires 30d;
      add_header Cache-Control "public, immutable";
  }
  ```
- Update HTML file references accordingly (e.g., `<img src="assets/image.png">`)
- Ensure image assets are reasonable file sizes (gzip compression applies to text, not images)

### Making Git Commits
After editing HTML/CSS/JavaScript files:
```bash
git status                    # See what changed
git add index.html            # Stage specific file(s)
git commit -m "feat: update hero section styling"
```

Common commit patterns for this project:
- `feat: add new section or feature`
- `fix: correct broken link or styling issue`
- `style: update colors or typography`
- `refactor: reorganize HTML structure`
- `docs: update CLAUDE.md or README`

## Deployment

### Docker Build & Deploy

The project includes a `Dockerfile` for containerization using `nginx:alpine`:

```bash
# Build the Docker image locally
docker build -t neosale-lp:latest .

# Run the container
docker run -p 8080:80 neosale-lp:latest
# Access at http://localhost:8080
```

**Build process**:
1. Copies custom `nginx.conf` to `/etc/nginx/conf.d/default.conf`
2. Copies all HTML/CSS/JavaScript/assets to `/usr/share/nginx/html`
3. Removes `Dockerfile` and `nginx.conf` from served files (security)
4. Exposes port 80
5. Runs nginx in foreground mode (required for Docker)

### Push to Docker Hub

To push to Docker Hub (requires authentication):

```bash
docker tag neosale-lp:latest brunobspaiva/neosale-lp:latest
docker push brunobspaiva/neosale-lp:latest
```

The parent CLAUDE.md lists the full monorepo deployment strategy (Docker Hub registry at `brunobspaiva/` prefix).

## Troubleshooting

| Issue | Solution |
|-------|----------|
| HTTP server not running | Use `python -m http.server 8000` or `npx http-server` from project root |
| Port 8000 already in use | Use different port: `python -m http.server 9000` |
| CSS changes not appearing | Hard refresh browser (Ctrl+Shift+R or Cmd+Shift+R) to clear cache |
| Images not loading | Check file path is correct and image exists; use relative paths only (no `../`) |
| JavaScript not executing | Check browser console for errors; ensure `<script>` is at end of `<body>` |
| Docker build fails | Ensure Dockerfile and nginx.conf are in project root |
| Nginx serves wrong file | Verify `nginx.conf` uses correct `index` directive (should be `index.html`) |

## Browser Compatibility

This project uses modern CSS features:
- **CSS Grid** and **Flexbox** - layout
- **CSS Custom Properties (Variables)** - theming
- **CSS backdrop-filter** - glass morphism effects
- **CSS transforms** and **transitions** - animations

**Supported browsers**:
- Chrome/Edge 88+
- Firefox 88+
- Safari 15+

For older browser support, would require significant refactoring. Consider migrating to **neosale-site** (Next.js) for broader compatibility.

## Maintenance Notes

- **No build step**: All changes are reflected immediately (no compilation needed)
- **No dependencies**: No npm packages, package.json, or node_modules
- **SEO-friendly**: Static HTML is ideal for search engine indexing
- **Performance**: Nginx serves files efficiently with gzip compression (configured in nginx.conf)
- **Security**: Built-in security headers protect against XSS, clickjacking, and MIME type sniffing
- **Legacy status**: This is a maintenance-mode project; new features should go in **neosale-site** or **neosale-lp-maya**

## When to Migrate

If you need to add any of these features, consider migrating to a modern framework:
- **Dynamic content** from database or API
- **Real-time updates** or WebSocket communication
- **User authentication** or session management
- **Server-side rendering** or dynamic routing
- **Build-time optimizations** (minification, code splitting)

Consider these alternatives:
- **neosale-site**: Modern Next.js 15 marketing site with full framework features
- **neosale-lp-maya**: Interactive Vite + React landing page with AI chat integration
- **neosale-mkt**: Next.js marketing app with authentication

Refer to the parent `/CLAUDE.md` for detailed monorepo architecture.
