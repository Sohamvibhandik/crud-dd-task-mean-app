
# CRUD MEAN Application — Dockerized Deployment with CI/CD

Repository: https://github.com/Sohamvibhandik/crud-dd-task-mean-app  
Live application (EC2 public IP): http://3.111.41.127  
Public DNS: http://ec2-3-111-41-127.ap-south-1.compute.amazonaws.com

---

## Summary

This repository contains a full-stack CRUD application implemented with the MEAN stack (MongoDB, Express, Angular, Node.js).  
The application is containerized with Docker (frontend, backend, database), orchestrated with Docker Compose, reverse-proxied by Nginx, and deployed on an Ubuntu EC2 instance. Continuous integration builds and publishes Docker images to Docker Hub, and Watchtower on the VM automatically pulls updated images and restarts containers.

The delivered solution includes:
- Dockerfiles for frontend and backend
- Docker Compose file to run the whole stack on a VM
- Nginx reverse-proxy configuration with SPA fallback for Angular deep links
- GitHub Actions workflow to build and push Docker images
- Watchtower on the VM to auto-deploy `latest` image updates
- Step-by-step README and screenshots demonstrating the pipeline and deployment

---

## Key artifacts (what to review)

- `backend/Dockerfile` — Node/Express app containerization
- `frontend/Dockerfile` — Angular build then nginx image
- `docker-compose.yml` — Compose file used on EC2 (pulls images from Docker Hub)
- `nginx/nginx.conf` — Reverse proxy configuration (proxies `/api/` to backend and serves the frontend with SPA fallback)
- `.github/workflows/ci-cd.yml` — GitHub Actions: builds images and pushes to Docker Hub
- `screenshots/` — Visual proof of CI runs, Docker Hub images, VM container status and UI

Docker Hub images used (pushed by CI):
- `sohamvibhandik/crud-mean-frontend:latest`
- `sohamvibhandik/crud-mean-backend:latest`

---

## Repository structure

crud-dd-task-mean-app/
├─ backend/ # Node.js + Express API + backend Dockerfile
├─ frontend/ # Angular app + frontend Dockerfile + nginx.conf (SPA fallback)
├─ nginx/ # Reverse proxy config used by EC2
├─ .github/workflows/ # GitHub Actions CI pipeline (build & push)
├─ docker-compose.yml # Compose for deployment (pulls from Docker Hub)
├─ screenshots/ # Required screenshots for submission
└─ README.md # This file

yaml
Copy code

---

## How it works — high level

1. Developer pushes code to the `main` branch.  
2. GitHub Actions builds frontend & backend Docker images and pushes them to Docker Hub.  
3. Watchtower running on the EC2 VM monitors containers with the Watchtower label; when it finds a new `latest` image on Docker Hub it pulls and restarts the affected container(s).  
4. Nginx on the VM exposes the application on port 80 and proxies `/api` calls to the backend; the frontend uses an SPA fallback (`try_files $uri $uri/ /index.html`) so deep links like `/tutorials` work.

---

## How to verify the deployment (quick checks)

### From your local machine (replace `<IP>` if needed)
```bash
# homepage
curl -I http://3.111.41.127

# deep link (SPA route)
curl -I http://3.111.41.127/tutorials
Both should return HTTP/1.1 200 OK.

On the EC2 VM (SSH into VM)
bash
Copy code
# in ~/deploy (repo)
docker compose pull
docker compose up -d

# confirm containers
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"

# check logs to confirm backend is connected to MongoDB and frontend served
docker logs backend --tail 50
docker logs frontend --tail 50
docker logs nginx --tail 50
Check Watchtower logs (confirm automatic update behavior)
bash
Copy code
docker logs watchtower --tail 80
How to run locally (developer convenience)
Backend

bash
Copy code
cd backend
npm install
npm start
# default port 8080
Frontend (development)

bash
Copy code
cd frontend
npm install
ng serve --host 0.0.0.0 --port 4200
Full stack with Docker Compose (local / VM)

bash
Copy code
docker compose up -d
Nginx reverse-proxy notes
nginx/nginx.conf (used by the VM) must include:

Proxy /api/ requests to http://backend:8080/.

Serve static files for frontend and fallback to /index.html for SPA deep links:

nginx
Copy code
location / {
  root /usr/share/nginx/html;
  try_files $uri $uri/ /index.html;
}
