# Level 3 Security Labs: Local Runner & Download Guide

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (SETUP_GUIDE.ar.md)](./SETUP_GUIDE.ar.md)

---

## 🚀 1. Zero-Setup Local Execution

All Level 3 labs are engineered to run **100% locally** in your web browser with zero npm builds, zero Docker containers, and zero external cloud dependencies.

### Option A: Direct Browser File Execution
You can open any lab directly:
- **Windows:** Double-click `index.html` or run in PowerShell:
  ```powershell
  Start-Process chrome.exe "E:\Programming Languages\02-HTML-CSS\Level-3-Advanced-and-Security\Labs\Lab-1-Guided-Walkthrough\index.html"
  ```
- **Linux / macOS:**
  ```bash
  xdg-open index.html
  ```

### Option B: Local Lightweight HTTP Server (Recommended)
Starting a local HTTP server prevents local `file://` origin security restrictions:
- **Using Python (Windows / Linux):**
  ```bash
  # Inside the lab folder
  python -m http.server 8080
  ```
  Then open: `http://localhost:8080`
- **Using VS Code Live Server:**
  Right-click `index.html` and select **"Open with Live Server"**.

---

## 📥 2. How to Download ONLY This Lab Folder from GitHub

If you only want this specific lab directory without downloading the entire repository, use any of these methods:

### Method 1: Web Downloader (Fastest & Easiest)
1. Go to **[download-directory.github.io](https://download-directory.github.io)**
2. Paste the GitHub directory URL:
   ```
   https://github.com/0xHaxmed/Programming-Languages/tree/main/02-HTML-CSS/Level-3-Advanced-and-Security/Labs
   ```
3. Press **Enter** to download a ready-to-use `.zip` archive of only this folder!

### Method 2: Git Sparse Checkout (For Git Users)
```bash
# Initialize empty repo
git init html-css-labs && cd html-css-labs

# Configure remote repository
git remote add -f origin https://github.com/0xHaxmed/Programming-Languages.git

# Enable sparse checkout
git config core.sparseCheckout true

# Specify only the Level 3 Labs directory
echo "02-HTML-CSS/Level-3-Advanced-and-Security/Labs/*" >> .git/info/sparse-checkout

# Pull only the selected folder
git pull origin main
```

### Method 3: Using Node / degit
```bash
npx degit 0xHaxmed/Programming-Languages/02-HTML-CSS/Level-3-Advanced-and-Security/Labs my-security-labs
```

---

## 🧪 Available Labs in this Level:
1. 🧪 **[Lab 1: Guided Walkthrough — Dangling Markup Token Theft](./Lab-1-Guided-Walkthrough/README.md)** (Fully explained and solved)
2. 🎯 **[Lab 2: Unsolved Challenge — CSS Data Exfiltration](./Lab-2-Challenge-Unsolved/README.md)** (Student challenge with report template)
