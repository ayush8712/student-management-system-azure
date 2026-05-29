# GitHub Actions - Azure Deployment Setup

This guide explains how to set up automated GitHub Actions workflow for continuous deployment to Azure App Service.

## Overview

The workflow in `.github/workflows/azure-deploy.yml` automatically:
- Builds your Node.js application on every push
- Installs production dependencies
- Deploys to Azure App Service
- Runs on `main` or `master` branch

## Setup Instructions

### Step 1: Create GitHub Repository

1. Go to [GitHub.com](https://github.com)
2. Click "New" to create a new repository
3. Name it: `student-task-manager`
4. Select "Public" (easier to share with teacher)
5. Click "Create repository"

### Step 2: Push Code to GitHub

In PowerShell/Command Prompt in your project folder:

```bash
# Initialize git (if not already done)
git init
git add .
git commit -m "Initial commit: Student Task Manager"

# Add GitHub as remote
git remote add origin https://github.com/YOUR_USERNAME/student-task-manager.git

# Push to GitHub
git branch -M main
git push -u origin main
```

### Step 3: Get Azure Publish Profile

1. Go to [Azure Portal](https://portal.azure.com)
2. Navigate to your App Service (e.g., `your-name-taskmanager`)
3. Click "Get publish profile" button (top-right)
   - A file will download: `your-name-taskmanager.PublishSettings`
4. Open the downloaded file with Notepad
5. Copy the ENTIRE content (Ctrl+A, Ctrl+C)

### Step 4: Add GitHub Secrets

1. Go to your GitHub repository
2. Click "Settings" (top menu)
3. Click "Secrets and variables" → "Actions"
4. Click "New repository secret"

#### Add Secret 1: AZURE_APP_NAME
- **Name**: `AZURE_APP_NAME`
- **Value**: Your app service name (e.g., `ayushtaskmanager-taskmanager`)
- Click "Add secret"

#### Add Secret 2: AZURE_PUBLISH_PROFILE
- **Name**: `AZURE_PUBLISH_PROFILE`
- **Value**: Paste the entire publish profile content from Step 3
- Click "Add secret"

### Step 5: Verify Workflow

1. Go to "Actions" tab in your GitHub repository
2. You should see "Build and Deploy to Azure App Service" workflow
3. It should be running (blue indicator)
4. Wait for it to complete (green checkmark = success)

## What Happens on Each Push

```
You commit & push code to main branch
         ↓
GitHub Actions workflow triggers automatically
         ↓
Step 1: Checkout code
Step 2: Setup Node.js environment
Step 3: Install npm dependencies
Step 4: Create deployment package
Step 5: Deploy to Azure App Service
         ↓
Your app is LIVE! ✅
```

## Testing the Workflow

1. Make a small change to your code (e.g., edit index.html title)
2. Commit and push:
   ```bash
   git add .
   git commit -m "Update title"
   git push
   ```
3. Go to GitHub "Actions" tab
4. Watch the workflow run
5. Check your Azure app URL to see the changes

## Troubleshooting Workflow Issues

### Issue: Workflow fails with "authentication failed"
- **Fix**: Re-generate publish profile and update AZURE_PUBLISH_PROFILE secret

### Issue: "App service not found"
- **Fix**: Verify AZURE_APP_NAME is correct (must match your Azure app service name exactly)

### Issue: "npm install fails"
- **Fix**: Check that all required dependencies are in package.json

### View Workflow Logs
1. Click on the failed workflow run
2. Click "Build-and-deploy" job
3. Click on any step to see detailed logs

## Manual Deployment (Without GitHub Actions)

If you want to deploy manually without GitHub Actions:

```bash
# Zip your application
$source = "path/to/project"
$destination = "app.zip"
Compress-Archive -Path "$source\*" -DestinationPath $destination -Force

# Deploy via Azure CLI
az webapp deployment source config-zip `
  --resource-group studentapp-rg `
  --name YOUR_APP_NAME `
  --src $destination
```

## Showing Your Teacher

You can show your teacher:
1. **GitHub Repository**: GitHub page with code
2. **GitHub Actions Tab**: Show workflow history and successful deployments
3. **Live App**: The deployed app running on Azure
4. **Azure Portal**: Show App Service running and receiving traffic

Example URLs to share:
- GitHub repo: `https://github.com/YOUR_USERNAME/student-task-manager`
- GitHub Actions: `https://github.com/YOUR_USERNAME/student-task-manager/actions`
- Live app: `https://your-app-name.azurewebsites.net`

## Disabling Auto-Deployment

To temporarily stop auto-deployment:
1. Go to GitHub repo → Settings → Actions
2. Select "Disable" (or configure conditions)

To re-enable:
1. Same location, click "Enable"

## Advanced: Environment-Specific Deployments

To deploy to different Azure environments (staging/production):

```yaml
# Add to azure-deploy.yml
env:
  AZURE_APP_NAME: ${{ secrets.AZURE_APP_NAME }}
  AZURE_ENVIRONMENT: production
```

Then create separate secrets for staging/prod apps.

---

**Your automatic deployment pipeline is now ready!** 🚀
On next push, your changes will automatically deploy to Azure.
