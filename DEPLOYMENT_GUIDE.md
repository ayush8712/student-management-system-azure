# Student Task Manager - Azure Deployment Guide

## Project Overview
A full-stack task management application with Express.js backend and responsive frontend. Features include adding tasks, marking as complete, filtering, and managing student assignments.

---

## PART 1: LOCAL SETUP & TESTING

### Step 1: Install Dependencies
```bash
# Navigate to your project directory
cd c:\Users\ayush\OneDrive\Desktop\studentmanagement

# Install Node.js packages
npm install
```

### Step 2: Run Locally
```bash
# Start the server
npm start
```
You should see: `Server is running on http://localhost:3000`

### Step 3: Test Locally
- Open browser: `http://localhost:3000`
- Add some test tasks
- Verify all features work (add, complete, delete)

---

## PART 2: PREPARE FOR AZURE DEPLOYMENT

### Step 1: Create Azure Account
1. Go to [Azure for Students](https://azure.microsoft.com/en-us/free/students/)
2. Sign in with your student email (@student.edu or similar)
3. Verify your student status
4. Get your free $100 credits

### Step 2: Install Required Tools
Download and install:
- **Azure CLI**: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows
- **Git**: https://git-scm.com/download/win

### Step 3: Initialize Git Repository
```bash
# Open PowerShell/Command Prompt in your project folder
cd c:\Users\ayush\OneDrive\Desktop\studentmanagement

# Initialize git
git init

# Add all files
git add .

# Create first commit
git commit -m "Initial commit: Student Task Manager"
```

---

## PART 3: DEPLOY TO AZURE (STEP-BY-STEP)

### Step 1: Login to Azure CLI
```bash
# Login to your Azure account
az login

# You'll be redirected to browser for authentication
# After login, return to terminal
```

### Step 2: Create Azure Resource Group
```bash
# Create a resource group (region: East US - most affordable)
az group create --name studentapp-rg --location eastus
```

### Step 3: Create App Service Plan
```bash
# Create a free App Service Plan (F1 = Free tier)
az appservice plan create `
  --name studentapp-plan `
  --resource-group studentapp-rg `
  --sku F1 `
  --is-linux
```

### Step 4: Create Web App
```bash
# Create the web app (must be globally unique)
# Replace YOUR_NAME with something unique (e.g., ayushtaskmanager)
az webapp create `
  --resource-group studentapp-rg `
  --plan studentapp-plan `
  --name YOUR_NAME-taskmanager `
  --runtime "NODE|18-lts"
```

### Step 5: Configure App Settings
```bash
# Set Node environment to production
az webapp config appsettings set `
  --resource-group studentapp-rg `
  --name YOUR_NAME-taskmanager `
  --settings WEBSITES_PORT=3000 NODE_ENV=production
```

### Step 6: Deploy Code to Azure

#### Option A: Deploy via Zip Upload (EASIEST)
```bash
# Create a zip file of your project
# In PowerShell:
$source = "c:\Users\ayush\OneDrive\Desktop\studentmanagement"
$destination = "c:\Users\ayush\OneDrive\Desktop\studentmanagement.zip"
Compress-Archive -Path "$source\*" -DestinationPath $destination -Force

# Deploy the zip
az webapp deployment source config-zip `
  --resource-group studentapp-rg `
  --name YOUR_NAME-taskmanager `
  --src $destination
```

#### Option B: Deploy via Git (Alternative)
```bash
# Configure git deployment
az webapp deployment user set --user-name YOUR_EMAIL@example.com --password YOUR_STRONG_PASSWORD

# Get deployment URL
$deploymentUrl = az webapp deployment source config-local-git `
  --resource-group studentapp-rg `
  --name YOUR_NAME-taskmanager `
  --query url --output tsv

# Add Azure as remote
git remote add azure $deploymentUrl

# Push to Azure
git push azure master
# When prompted, enter the password you set above
```

### Step 7: Monitor Deployment
```bash
# Check deployment status
az webapp deployment list-publishing-profiles `
  --resource-group studentapp-rg `
  --name YOUR_NAME-taskmanager `
  --query "[0].destinationAppUrl" `
  --output tsv
```

### Step 8: View Live Application
Your app will be available at:
```
https://YOUR_NAME-taskmanager.azurewebsites.net
```

---

## PART 3B: AUTOMATIC DEPLOYMENT (OPTIONAL - GitHub Actions)

For automatic deployment on every code push, set up GitHub Actions:

1. Create GitHub repository for your code
2. Follow [GITHUB_ACTIONS_SETUP.md](GITHUB_ACTIONS_SETUP.md)
3. Now every `git push` automatically deploys to Azure!

Benefits:
- ✅ Automatic deployments
- ✅ No manual zip uploads needed
- ✅ Professional CI/CD pipeline
- ✅ Great to show your teacher

---

## PART 4: VERIFY & TEST DEPLOYMENT

1. Open your Azure URL in browser
2. Test all features:
   - ✓ Add a new task
   - ✓ Mark task as complete
   - ✓ Delete a task
   - ✓ Filter by All/Active/Completed
   - ✓ Set priority and due date

---

## PART 5: SHOW TO YOUR TEACHER

### What to Demonstrate:
1. **Application Features**
   - Live task management functionality
   - Responsive design (works on mobile/desktop)
   - Real-time updates

2. **Azure Dashboard**
   - Show Resource Group
   - Show App Service running
   - Show metrics/activity

3. **Code Quality**
   - Backend: REST API with CRUD operations
   - Frontend: Modern UI with filters
   - Proper error handling

### Azure Portal URL to Show:
```
https://portal.azure.com
```

---

## PART 6: MONITOR & MANAGE

### View Application Logs
```bash
az webapp log tail `
  --resource-group studentapp-rg `
  --name YOUR_NAME-taskmanager
```

### Check Application Status
```bash
az webapp show `
  --resource-group studentapp-rg `
  --name YOUR_NAME-taskmanager `
  --query state
```

### Stop/Start Application
```bash
# Stop
az webapp stop --resource-group studentapp-rg --name YOUR_NAME-taskmanager

# Start
az webapp start --resource-group studentapp-rg --name YOUR_NAME-taskmanager
```

---

## PART 7: CLEANUP (When Assessment is Done)

```bash
# Delete everything (to not waste free credits)
az group delete --name studentapp-rg --yes
```

---

## TROUBLESHOOTING

### Issue: "Resource already exists"
- Use a different name for the web app (must be unique globally)

### Issue: "Port already in use"
- The App Service automatically manages the port. Don't worry!

### Issue: "Application is offline"
- Check logs: `az webapp log tail --resource-group studentapp-rg --name YOUR_NAME-taskmanager`
- Restart app: `az webapp restart --resource-group studentapp-rg --name YOUR_NAME-taskmanager`

### Issue: "502 Bad Gateway"
- Wait 2-3 minutes for deployment to complete
- Check that PORT is set to 3000 in app settings

---

## FREQUENTLY ASKED QUESTIONS

**Q: Will this cost money?**
A: No! Students get $100 free credits per year, and App Service F1 (Free tier) is included.

**Q: How long will deployment take?**
A: Usually 2-5 minutes from upload to live.

**Q: Can I update the app after deployment?**
A: Yes! Zip and re-upload, or git push again.

**Q: What if I make changes locally?**
A: Either zip-upload again or git push azure master

---

## QUICK COMMAND SUMMARY

```bash
# Login
az login

# Create resources
az group create --name studentapp-rg --location eastus
az appservice plan create --name studentapp-plan --resource-group studentapp-rg --sku F1 --is-linux
az webapp create --resource-group studentapp-rg --plan studentapp-plan --name YOUR_NAME-taskmanager --runtime "NODE|18-lts"

# Deploy (zip method)
Compress-Archive -Path "c:\Users\ayush\OneDrive\Desktop\studentmanagement\*" -DestinationPath "app.zip" -Force
az webapp deployment source config-zip --resource-group studentapp-rg --name YOUR_NAME-taskmanager --src app.zip

# View app
https://YOUR_NAME-taskmanager.azurewebsites.net

# Cleanup
az group delete --name studentapp-rg --yes
```

---

**Good luck with your assessment! 🚀**
