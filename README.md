# MEAN CRUD Application

This is a simple **MEAN (MongoDB, Express, Angular, Node.js)** CRUD application with Docker, Git, and GitHub integration.

## Project Structure

```
mean-crud-app/
├── backend/
│   ├── index.js
│   ├── package.json
│   ├── Dockerfile
├── frontend/
│   └── frontend-app/
│       ├── src/
│       ├── angular.json
│       ├── package.json
│       └── Dockerfile
├── docker-compose.yml
├── README.md
```

---

## Step 1: Clone Repository or Create Directory

```bash
mkdir mean-crud-app
cd mean-crud-app
```

---

## Step 2: Setup Backend

### `backend/index.js`

```js
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');

const app = express();
const PORT = 3000;

app.use(cors());
app.use(express.json());

mongoose.connect('mongodb://mongo:27017/mean-crud', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

const TaskSchema = new mongoose.Schema({
  title: String,
  description: String,
});

const Task = mongoose.model('Task', TaskSchema);

app.get('/tasks', async (req, res) => {
  const tasks = await Task.find();
  res.json(tasks);
});

app.post('/tasks', async (req, res) => {
  const task = new Task(req.body);
  await task.save();
  res.json(task);
});

// Optional root route
app.get('/', (req, res) => {
  res.send('API is running...');
});

app.listen(PORT, () => {
  console.log(`Backend running on port ${PORT}`);
});
```

### `backend/package.json`

```json
{
  "name": "backend",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "body-parser": "^2.2.0",
    "cors": "^2.8.5",
    "express": "^5.1.0",
    "mongoose": "^8.13.2"
  }
}
```

### `backend/Dockerfile`

```Dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "index.js"]
```

---

## Step 3: Setup Frontend

Navigate to `frontend/` and create Angular app:

```bash
cd frontend
ng new frontend-app
cd frontend-app
```

### `frontend/frontend-app/Dockerfile`

```Dockerfile
# Stage 1: Build
FROM node:18 as build

WORKDIR /app
COPY package*.json ./
RUN npm install -g @angular/cli && npm install
COPY . .
RUN ng build --configuration=production

# Stage 2: Serve with Nginx
FROM nginx:alpine

COPY --from=build /app/dist/frontend-app /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## Step 4: Create `docker-compose.yml`

```yaml
services:
  mongo:
    image: mongo
    container_name: mean-crud-app-mongo
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

  backend:
    build: ./backend
    container_name: mean-crud-app-backend
    ports:
      - "3000:3000"
    depends_on:
      - mongo

  frontend:
    build: ./frontend/frontend-app
    container_name: mean-crud-app-frontend
    ports:
      - "4200:80"
    depends_on:
      - backend

volumes:
  mongo-data:
```

---

## Step 5: Run the App

```bash
docker-compose up --build
```

- Backend → [http://localhost:3000/tasks](http://localhost:3000/tasks)
- ![Screenshot 2025-04-05 231013](https://github.com/user-attachments/assets/c8229363-98a1-4680-bf9a-72d8b48f60cf)


- Frontend → [http://localhost:4200](http://localhost:4200)
- ![Screenshot 2025-04-05 223246](https://github.com/user-attachments/assets/256aa75d-4dbf-4637-9e5f-3a23df9c5734)


---

## Step 6: GitHub Integration

```bash
git init
git remote add origin https://github.com/YourUsername/mean-crud-app.git
git add .
git commit -m "Initial commit"
git pull origin main --allow-unrelated-histories
git push -u origin main
```

If you face authentication errors, prefer using **Personal Access Tokens** over your password.

---

## Optional Fix: Remove Embedded Git in Angular Folder

```bash
cd frontend/frontend-app
rm -rf .git
cd ../..
git add .
git commit -m "Removed embedded git"
```

---

## Done ✅

You now have a fully containerized MEAN CRUD app, versioned in GitHub, and running via Docker!

