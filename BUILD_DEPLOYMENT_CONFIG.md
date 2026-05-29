# Build & Deployment Configuration Guide

This guide explains all the deployment and configuration files added to your project.

## 📁 Configuration Files Overview

### 1. `.github/workflows/azure-deploy.yml` ⭐ RECOMMENDED
**Purpose**: Automated CI/CD pipeline using GitHub Actions

**When to use**:
- ✅ You're using GitHub for version control
- ✅ You want automatic deployments on every push
- ✅ Free tier includes monthly free minutes
- ✅ Best for showing your teacher

**How it works**:
```
Push code to main branch
         ↓
GitHub automatically:
  • Checks out your code
  • Installs Node.js
  • Installs dependencies
  • Builds the app
  • Deploys to Azure
         ↓
Your app is updated live!
```

**Setup**: See [GITHUB_ACTIONS_SETUP.md](GITHUB_ACTIONS_SETUP.md)

---

### 2. `azure-pipelines.yml`
**Purpose**: Azure DevOps CI/CD pipeline (alternative to GitHub Actions)

**When to use**:
- ✅ You prefer Azure DevOps over GitHub Actions
- ✅ Your company/school uses Azure DevOps
- ✅ You want more advanced pipeline features

**How it works**:
- Same as GitHub Actions but runs on Azure DevOps
- Requires Azure DevOps account setup

**Setup**: 
1. Go to [dev.azure.com](https://dev.azure.com)
2. Create new project
3. Create pipeline from this file

---

### 3. `web.config`
**Purpose**: IIS/Azure App Service configuration for Node.js

**What it does**:
- Configures IIS to handle Node.js requests properly
- Routes all requests through your Express server
- Enables logging for debugging
- Sets up static file caching for better performance
- Protects sensitive folders (node_modules, logs)

**Why it's needed**:
- Azure App Service uses IIS (Internet Information Services) on Windows
- Node.js applications need special configuration
- Ensures your Express routes work correctly

---

### 4. `package.json`
**Purpose**: Node.js project configuration

**Includes**:
- Project metadata (name, version, description)
- Dependencies (Express, CORS, Body-parser)
- Start scripts (npm start)

**Key section**:
```json
"scripts": {
  "start": "node server.js",
  "dev": "node server.js"
}
```

---

### 5. `server.js`
**Purpose**: Express.js backend server

**Features**:
- REST API endpoints for task management
- CORS enabled for cross-origin requests
- Static file serving (public folder)
- In-memory data storage
- Error handling

**API Endpoints**:
- `GET /api/tasks` - Get all tasks
- `POST /api/tasks` - Create new task
- `PUT /api/tasks/:id` - Update task
- `DELETE /api/tasks/:id` - Delete task
- `GET /api/health` - Health check

---

## 🚀 Deployment Options Comparison

| Feature | Manual ZIP | GitHub Actions | Azure DevOps |
|---------|-----------|-----------------|--------------|
| **Setup Time** | 5 min | 15 min | 20 min |
| **Automatic Deploy** | ❌ | ✅ | ✅ |
| **Cost** | Free | Free | Free |
| **Ease of Use** | Easy | Easy | Medium |
| **For Students** | ✅ | ✅✅✅ | ✅ |

---

## 📊 Deployment Workflow (You Choose One)

### Option 1: Manual Deployment (Simplest - but repetitive)

```powershell
# Edit code locally
# Test locally (npm start)
# Create ZIP
Compress-Archive -Path "c:\path\to\project\*" -DestinationPath "app.zip" -Force

# Deploy
az webapp deployment source config-zip `
  --resource-group studentapp-rg `
  --name YOUR_APP_NAME `
  --src app.zip

# App updates in 2-5 minutes
```

**Pros**: Simple, immediate
**Cons**: Manual every time, boring to show teacher

---

### Option 2: GitHub Actions (Recommended) ⭐

```bash
# Edit code locally
# Test locally (npm start)
git add .
git commit -m "Your message"
git push

# GitHub Actions automatically:
#   • Builds app
#   • Deploys to Azure
#   • App updates in 2-5 minutes
```

**Pros**: Automatic, professional, great for teacher demo
**Cons**: 15 min initial setup

---

### Option 3: Azure DevOps (Alternative)

```bash
# Same as GitHub Actions
git push

# Azure DevOps pipeline automatically deploys
```

**Pros**: Integrates with Azure ecosystem
**Cons**: More complex setup

---

## 🔧 How to Use Each Configuration

### Using GitHub Actions (Recommended for Assessment)

1. **Initial Setup (One time)**
   ```bash
   # Create GitHub repo and push code
   git remote add origin https://github.com/YOUR_USERNAME/student-task-manager.git
   git push -u origin main
   ```

2. **Add Azure Credentials**
   - Get publish profile from Azure Portal
   - Add GitHub secrets (AZURE_APP_NAME, AZURE_PUBLISH_PROFILE)
   - See [GITHUB_ACTIONS_SETUP.md](GITHUB_ACTIONS_SETUP.md)

3. **From Then On**
   ```bash
   # Make changes
   git add .
   git commit -m "message"
   git push
   
   # ✅ Auto-deploys! No extra steps!
   ```

4. **Show Your Teacher**
   - GitHub repo code
   - GitHub Actions tab (shows deployment history)
   - Live app on Azure
   - "It deploys automatically on every push!"

---

### Using web.config (For Azure)

Already configured! You don't need to do anything special. But know that:
- It's automatically used by Azure App Service
- Handles Node.js request routing
- Enables logging at `D:\home\LogFiles\nodejs`

---

## 📝 Environment Variables

If you add environment variables, set them in Azure:

```bash
# Using Azure CLI
az webapp config appsettings set `
  --resource-group studentapp-rg `
  --name YOUR_APP_NAME `
  --settings "NODE_ENV=production" "LOG_LEVEL=info"
```

Or in Azure Portal:
1. Your App Service → Configuration
2. Click "New application setting"
3. Enter name and value
4. Save

---

## 🔍 Monitoring Deployments

### GitHub Actions
```
Your repo → Actions tab → Click workflow run
See build logs, deployment status, errors
```

### Azure
```
Azure Portal → Your App Service → Activity log
Or: az webapp log tail --resource-group ... --name ...
```

---

## ❌ Troubleshooting

### "Workflow fails at build stage"
- Check logs in GitHub Actions
- Ensure all dependencies are in package.json
- Run `npm install` locally to verify

### "Deployment succeeds but app offline"
- Check web.config is valid
- Verify PORT environment variable is 3000
- Check Azure logs: `az webapp log tail`

### "GitHub Actions workflow not triggered"
- Make sure file is in `.github/workflows/`
- Commit and push the workflow file itself
- Workflows only trigger on main/master branch

### "web.config causes 500 error"
- The web.config provided is standard for Node.js
- If issues persist, Azure support can help

---

## 📚 Resources

- **GitHub Actions**: https://docs.github.com/en/actions
- **Azure App Service**: https://learn.microsoft.com/en-us/azure/app-service/
- **Azure DevOps**: https://dev.azure.com

---

## ✅ Recommended Setup for Your Assessment

1. **Use GitHub Actions** (most professional)
2. **Follow GITHUB_ACTIONS_SETUP.md** (step-by-step)
3. **Push to GitHub** (show your teacher)
4. **Demonstrate auto-deployment** (very impressive!)

Your teacher will see:
✅ Working application
✅ Automated CI/CD
✅ Professional development workflow
✅ Understanding of DevOps

**Great for assessment demonstration!** 🎓
