# Deployment Architecture & Workflow

## Complete System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        YOUR DEVELOPMENT                             │
│                                                                     │
│  ┌──────────────┐                                                  │
│  │  Local Code  │                                                  │
│  │ Development  │                                                  │
│  └──────┬───────┘                                                  │
│         │                                                          │
│         │ npm install                                             │
│         │ npm start                                               │
│         │ Test locally ✅                                         │
│         │                                                          │
│         ▼                                                          │
│  ┌──────────────┐                                                  │
│  │  Git Commit  │                                                  │
│  │  git add .   │                                                  │
│  │  git commit  │                                                  │
│  └──────┬───────┘                                                  │
│         │                                                          │
│         └─────────────────────────────────────────────────┐        │
└────────────────────────────────────────────────────────────┼────────┘
                                                             │
                                                    git push │
                                                             │
                     ┌───────────────────────────────────────┘
                     │
                     ▼
    ┌────────────────────────────────────┐
    │        GITHUB REPOSITORY           │
    │                                    │
    │  📦 Your code (main branch)        │
    │     ├─ server.js                   │
    │     ├─ public/                     │
    │     ├─ package.json                │
    │     └─ ...                         │
    │                                    │
    └────────────────────────────────────┘
             │
             │ Push to main branch
             │ TRIGGERS WORKFLOW
             │
             ▼
    ┌────────────────────────────────────────────┐
    │    GITHUB ACTIONS CI/CD PIPELINE           │
    │                                            │
    │  Step 1: Checkout code        ✓ Running   │
    │  Step 2: Setup Node.js 18.x   ✓ Complete  │
    │  Step 3: npm install          ✓ Complete  │
    │  Step 4: Run tests            ✓ Complete  │
    │  Step 5: Build package        ✓ Complete  │
    │  Step 6: Deploy to Azure      ⏳ In Progress
    │                                            │
    └────────────────────────────────────────────┘
             │
             │ Deploy package
             │
             ▼
    ┌────────────────────────────────────┐
    │      AZURE APP SERVICE             │
    │                                    │
    │  🌐 your-app.azurewebsites.net   │
    │                                    │
    │  ┌──────────────────────────────┐ │
    │  │  Node.js Runtime             │ │
    │  │  - server.js running         │ │
    │  │  - Port 3000                 │ │
    │  │  - IIS handling requests     │ │
    │  └──────────────────────────────┘ │
    │                                    │
    │  📊 Activity Log                  │
    │  📈 Performance Metrics           │
    │  🔍 Application Logs              │
    │                                    │
    └────────────────────────────────────┘
             │
             │
             ▼
    ┌────────────────────────────────────┐
    │      YOUR LIVE APP! ✅             │
    │                                    │
    │  Users can access:                │
    │  • Add tasks                       │
    │  • Complete tasks                  │
    │  • Delete tasks                    │
    │  • View all tasks                  │
    │                                    │
    │  URL: your-app.azurewebsites.net  │
    │                                    │
    └────────────────────────────────────┘
```

---

## File Flow During Deployment

```
┌─────────────────────────────────────────────────────────────┐
│  Your GitHub Repository (After Push)                        │
│                                                             │
│  .github/workflows/azure-deploy.yml  ◄─── TRIGGERS         │
│                                                             │
│  Files being deployed:                                      │
│  • server.js ─────────┐                                    │
│  • package.json       │                                    │
│  • public/            │                                    │
│  • web.config         │                                    │
│                       │                                    │
└───────────────────────┼─────────────────────────────────────┘
                        │
                        │ GitHub Actions packages & deploys
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│  app.zip (Created by GitHub Actions)                       │
│                                                             │
│  ├─ server.js                                               │
│  ├─ package.json                                            │
│  ├─ package-lock.json (pinned versions)                    │
│  ├─ public/                                                 │
│  │  ├─ index.html                                           │
│  │  ├─ script.js                                            │
│  │  └─ style.css                                            │
│  ├─ web.config                                              │
│  └─ node_modules/ (production only)                        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
                        │
                        │ Uploaded to Azure
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│  Azure App Service Instance                                 │
│                                                             │
│  D:\home\site\wwwroot\                                     │
│  ├─ server.js (running)                                     │
│  ├─ package.json                                            │
│  ├─ package-lock.json                                       │
│  ├─ public/                                                 │
│  │  ├─ index.html                                           │
│  │  ├─ script.js                                            │
│  │  └─ style.css                                            │
│  ├─ web.config (IIS config)                                │
│  ├─ node_modules/ (installed)                              │
│  │                                                          │
│  └─ D:\home\LogFiles\nodejs\  (logs)                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
                        │
                        │ IIS routes requests to Node.js
                        │
                        ▼
                    Your App Live! ✅
```

---

## Deployment Sequence (Timeline)

```
Time    Event                          Status
────────────────────────────────────────────────────────

00:00   $ git push
        └─ Your code pushed to GitHub

00:01   🔔 Workflow triggered
        └─ .github/workflows/azure-deploy.yml

00:15   ✓ Checkout complete
        └─ GitHub Actions has your code

00:20   ✓ Node.js installed (v18.x)
        └─ Runtime environment ready

00:35   ✓ npm install complete
        └─ All dependencies installed

00:50   ✓ Tests complete (if any)
        └─ No errors detected

01:00   ✓ Build package created
        └─ app.zip ready to deploy

01:15   ⏳ Uploading to Azure
        └─ Package sent to App Service

02:30   ✓ Deployment complete
        └─ IIS starting Node.js

02:45   ✓ Application online
        └─ Ready to accept requests

03:00   ✅ LIVE!
        └─ Users can access your app
           https://your-app.azurewebsites.net

Total time: ~3 minutes from push to live! 🚀
```

---

## Request Handling Flow

```
User visits: https://your-app.azurewebsites.net

                        ▼
        
        Azure App Service (IIS)
        
        Receives HTTP request
        
                        ▼
        
        web.config routing rules
        
        Route static files OR
        Route to server.js
        
                        ▼
        
        server.js (Node.js/Express)
        
        ├─ GET /                  → Serve index.html
        ├─ GET /style.css         → Serve stylesheet
        ├─ GET /script.js         → Serve JavaScript
        ├─ GET /api/tasks         → Return JSON tasks
        ├─ POST /api/tasks        → Create new task
        ├─ PUT /api/tasks/:id     → Update task
        ├─ DELETE /api/tasks/:id  → Delete task
        └─ ...
        
                        ▼
        
        Response sent back to user
        
        Browser renders HTML
        JavaScript loads
        Styles applied
        
                        ▼
        
        ✅ User sees working application!
```

---

## Comparison: Deployment Methods

### Manual Deployment

```
Edit code
   ▼
npm start (local test)
   ▼
Compress-Archive (create ZIP)
   ▼
az webapp deployment source config-zip (manual upload)
   ▼
Wait 2-5 minutes
   ▼
Check if it worked
   ▼
Done! (until next change)
```

### GitHub Actions Deployment (Automatic) ⭐

```
Edit code
   ▼
git add .
   ▼
git commit
   ▼
git push ← That's it! Everything else is automatic
   ▼
GitHub Actions automatically:
  • Checks out code
  • Installs dependencies
  • Runs tests
  • Builds package
  • Deploys to Azure
   ▼
Deployed automatically!
(You can watch in Actions tab)
```

---

## Folder Structure After Deployment

```
Azure App Service Filesystem:

D:\home\
├─ site\
│  └─ wwwroot\  (Your app files)
│     ├─ server.js
│     ├─ package.json
│     ├─ package-lock.json
│     ├─ web.config
│     ├─ public\
│     │  ├─ index.html
│     │  ├─ style.css
│     │  └─ script.js
│     └─ node_modules\
│        ├─ express\
│        ├─ cors\
│        ├─ body-parser\
│        └─ ... (all npm packages)
│
└─ LogFiles\
   └─ nodejs\  (Application logs)
      └─ iisnode-YOUR_ID.log
```

---

## GitHub Actions Execution Environment

```
Ubuntu Linux Container (Provided by GitHub)

├─ Operating System: Ubuntu Latest
│
├─ Installed Tools:
│  ├─ Node.js 18.x LTS
│  ├─ npm / yarn
│  ├─ git
│  └─ Azure CLI (for deployment)
│
├─ Your Project Files: ✓ Checked out
│
├─ Execution Steps:
│  1. npm install → installs dependencies
│  2. npm test → runs tests (if any)
│  3. Creates deployment package
│  4. Authenticates with Azure
│  5. Deploys package to Azure
│
└─ Cleanup: Container destroyed after job
```

---

## Monitoring & Troubleshooting

```
Something went wrong?

Check GitHub Actions:
github.com/YOUR_USERNAME/student-task-manager/actions
                        │
                        ▼
                See which step failed
                        │
                        ▼
                Click failed step
                        │
                        ▼
                Read error message
                        │
                        ▼
                Fix the issue
                        │
                        ▼
                Push again
                        │
                        ▼
                New workflow automatically runs
```

Or check Azure Logs:

```bash
az webapp log tail `
  --resource-group studentapp-rg `
  --name your-app-name
```

---

**Your deployment architecture is now enterprise-grade!** 🎓
