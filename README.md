# Microservices Architecture — Interview Prep Guide

A systematic breadth-first guide to microservices design, patterns, and deployment for architects.

## Local Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/nitinkc/Microservices-Design-Architecture.git
   cd Microservices-Design-Architecture
   ```

2. **Create a virtual environment**
   ```bash
   python3 -m venv .venv
   ```

3. **Install dependencies**
   ```bash
   source .venv/bin/activate 
    # On Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```

4. **Start the local server**
   ```bash
   mkdocs serve
   ```
   The site will be available at `http://127.0.0.1:8000`

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