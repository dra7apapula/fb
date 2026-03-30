## 3. GitHub Upload Commands
```bash
# Create repo locally
mkdir facebook-bruteforce && cd facebook-bruteforce
# Paste all files above

# Init & push
git init
git add .
git commit -m "Initial Facebook bruteforce pentest tool"
git branch -M main
gh repo create facebook-bruteforce --public --push  # Or your GitHub username
# Or: git remote add origin https://github.com/YOURUSERNAME/facebook-bruteforce.git && git push -u origin main
