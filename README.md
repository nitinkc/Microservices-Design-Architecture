# Microservices Architecture — Interview Prep Guide

A systematic breadth-first guide to microservices design, patterns, and deployment for architects.

---

## Quick Start

### Prerequisites
- Python 3.8+
- Git

### Local Setup

1. **Clone the repository**
   ```bash
   git clone (https://github.com/nitinkc/Microservices-Design-Architecture.git)
   cd architectureStyle
   ```

2. **Create a virtual environment**
   ```bash
   python3 -m venv .venv
   ```

3. **Install dependencies**
   ```bash
   source .venv/bin/activate  # On Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```

4. **Start the local server**
   ```bash
   mkdocs serve
   ```
   The site will be available at `http://127.0.0.1:8000`

---

## Building for Production

To build the static site:

```bash
mkdocs build
```

Output will be generated in the `site/` directory.

---

## Deploying to GitHub Pages

### One-Time Setup

1. **Ensure your repository is on GitHub** with the remote named `origin`

2. **Configure GitHub Pages**
   - Go to your repository Settings → Pages
   - Set Source to **Deploy from a branch**
   - Select **gh-pages** branch as the deployment branch

3. **Install mkdocs GitHub Pages plugin** (optional, for easier deployment)
   ```bash
   pip install mkdocs-ghpages
   ```

### Deploy Steps

**Using mkdocs built-in GH Pages support:**

```bash
mkdocs gh-deploy
```

This will:
1. Build the site
2. Create/update a `gh-pages` branch
3. Push it to GitHub
4. Serve your site at `https://<username>.github.io/<repo-name>`

**Alternative: Manual deployment**

```bash
# Build the site
mkdocs build

# Commit and push
git add .
git commit -m "Update docs"
git push origin main

# Deploy to GitHub Pages
git subtree push --prefix site origin gh-pages
```

---

## Project Structure

```
architectureStyle/
├── mkdocs.yml              ← Site configuration
├── requirements.txt        ← Python dependencies
├── README.md               ← This file
├── docs/
│   ├── index.md                    ← Home page
│   ├── 01-foundations.md           ← Architectural Foundations
│   ├── 02-ddd.md                   ← Domain-Driven Design
│   ├── 03-microservices-patterns.md
│   ├── 04-event-driven.md
│   ├── 05-api-communication.md
│   ├── 06-resilience.md
│   ├── 07-observability.md
│   ├── 08-security.md
│   ├── 09-deployment.md
│   ├── 10-interview.md             ← Interview Prep Q&A
│   └── js/                         ← Mermaid diagram initialization
└── site/                           ← Generated static site (build output)
```

---

## Editing Content

All documentation is in Markdown format under `docs/`. To add or edit content:

1. Edit the `.md` files in `docs/`
2. Run `mkdocs serve` to preview changes
3. Commit and push to trigger GitHub Pages deployment (if set up with gh-deploy)

---

## Troubleshooting

**Port 8000 already in use:**
```bash
mkdocs serve --dev-addr=127.0.0.1:8001
```

**Virtual environment issues:**
```bash
# Deactivate current environment
deactivate

# Remove and recreate
rm -rf .venv
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

**GitHub Pages not updating:**
- Check the "Actions" tab in your GitHub repository for deployment status
- Ensure the `gh-pages` branch exists
- Clear your browser cache (Cmd+Shift+R on macOS)

---

## Learn More

- [MkDocs Documentation](https://www.mkdocs.org/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Copilot Instructions](file://.github/copilot-instructions.md) — Content guidelines for this project

