# Student Task Manager

A modern web application designed to help students manage their tasks, assignments, and deadlines efficiently. Built with Node.js/Express backend and a responsive frontend.

## Features

✨ **Task Management**
- Add tasks with title, description, priority, and due date
- Mark tasks as complete
- Delete tasks
- Filter tasks (All, Active, Completed)

🎨 **User Interface**
- Modern, responsive design
- Beautiful gradient styling
- Mobile-friendly layout
- Real-time task updates
- Priority badges and due date display

⚙️ **Backend API**
- RESTful API endpoints
- CORS enabled for cross-origin requests
- Error handling
- Health check endpoint

## Project Structure

```
student-task-manager/
├── server.js              # Express server and API routes
├── package.json           # Project dependencies
├── public/
│   ├── index.html        # Main HTML page
│   ├── style.css         # Styling
│   └── script.js         # Frontend JavaScript
├── .gitignore            # Git ignore file
├── README.md             # This file
└── DEPLOYMENT_GUIDE.md   # Azure deployment steps
```

## API Endpoints

### Get all tasks
```
GET /api/tasks
```

### Add new task
```
POST /api/tasks
Content-Type: application/json

{
  "title": "Assignment",
  "description": "Math homework",
  "priority": "High",
  "dueDate": "2026-06-01"
}
```

### Update task
```
PUT /api/tasks/:id
Content-Type: application/json

{
  "completed": true
}
```

### Delete task
```
DELETE /api/tasks/:id
```

### Health check
```
GET /api/health
```

## Installation & Local Development

### Prerequisites
- Node.js (v14 or higher)
- npm (comes with Node.js)

### Setup

1. **Clone or navigate to project folder**
   ```bash
   cd student-task-manager
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Start the server**
   ```bash
   npm start
   ```

4. **Open in browser**
   ```
   http://localhost:3000
   ```

## Technologies Used

- **Backend**: Node.js, Express.js
- **Frontend**: HTML5, CSS3, Vanilla JavaScript
- **Deployment**: Azure App Service
- **Version Control**: Git

## Browser Compatibility

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)

## Deployment

For detailed Azure deployment instructions, see [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md)

**Quick Deploy:**
```bash
az login
az group create --name studentapp-rg --location eastus
az appservice plan create --name studentapp-plan --resource-group studentapp-rg --sku F1 --is-linux
az webapp create --resource-group studentapp-rg --plan studentapp-plan --name YOUR_NAME-taskmanager --runtime "NODE|18-lts"
```

## Author

Created for student assignment management

## License

ISC

## Support

For issues or questions, check DEPLOYMENT_GUIDE.md troubleshooting section.
