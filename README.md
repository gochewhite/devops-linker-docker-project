# devops-linker-docker-project
# Docker Implementation for DevOps Bootcamp Linker Application

## Overview

This document describes the process of containerizing the **DevOps Bootcamp Linker Application**, which consists of:

* **Frontend:** Next.js application
* **Backend:** NestJS API with Prisma ORM
* **Database:** PostgreSQL

The goal was to create optimized Docker images, push them to Docker Hub, and orchestrate the services using **Docker Compose**.

---

# 1. Running the Application Locally

Before containerizing the application, both the frontend and backend were run locally to confirm they worked correctly.

### Backend

Steps:

1. Navigate to the backend directory:

   ```
   cd devops-bootcamp-linker-backend
   ```

2. Install dependencies:

   ```
   npm install
   ```

3. Configure environment variables in `.env`.

4. Run Prisma migrations:

   ```
   npx prisma migrate dev
   ```

5. Start the backend server:

   ```
   npm run start:dev
   ```

The backend runs on:

```
http://localhost:3001
```

---

### Frontend

Steps:

1. Navigate to the frontend directory:

```
cd devops-bootcamp-linker-frontend
```

2. Install dependencies:

```
npm install
```

3. Set the environment variable:

```
NEXT_PUBLIC_API_URL=http://localhost:3001/api
```

4. Start the development server:

```
npm run dev
```

The frontend runs on:

```
http://localhost:3000
```

---

# 2. Dockerizing the Applications

## Frontend Dockerfile

The frontend Dockerfile uses a **multi-stage build** to reduce image size and create a production-ready image.

Key features:

* Node Alpine base image
* Multi-stage build
* Non-root user for security
* Optimized production build

Example stages:

**Builder Stage**

* Installs dependencies
* Builds the Next.js application

**Runtime Stage**

* Runs the optimized production build

---

## Backend Dockerfile

The backend Dockerfile also uses **multi-stage builds**.

### Builder Stage

Responsible for:

* Installing build dependencies
* Installing Node dependencies
* Generating Prisma client
* Building the NestJS application

### Runtime Stage

Responsible for:

* Installing production dependencies only
* Running the compiled NestJS application
* Using a non-root user
* Running a startup script that waits for PostgreSQL before starting the backend

This approach keeps the image **small, secure, and production-ready**.

---

# 3. Pushing Docker Images to Docker Hub

After building the images locally, they were tagged and pushed to Docker Hub.

### Login to Docker Hub

```
docker login
```

### Build Frontend Image

```
docker build -t bootcamp-linker-frontend .
```

### Tag Frontend Image

```
docker tag bootcamp-linker-frontend whiteobah/bootcamp-linker-frontend:latest
```

### Push Frontend Image

```
docker push whiteobah/bootcamp-linker-frontend:latest
```

---

### Build Backend Image

```
docker build -t bootcamp-linker-backend .
```

### Tag Backend Image

```
docker tag bootcamp-linker-backend whiteobah/bootcamp-linker-backend:latest
```

### Push Backend Image

```
docker push whiteobah/bootcamp-linker-backend:latest
```

---

# 4. Docker Compose Configuration

Docker Compose was used to orchestrate the **frontend, backend, and PostgreSQL database**.

The compose file defines three services:

* **postgres**
* **backend**
* **frontend**

### Key Features

* Automatic service networking
* Database volume persistence
* Service dependency ordering
* Environment variable configuration

Example structure:

* PostgreSQL database container
* NestJS backend container connected to PostgreSQL
* Next.js frontend container connected to backend API

All services are started with:

```
docker compose up
```

---

# 5. Running the Application with Docker

Start the containers:

```
docker compose up
```

Check running containers:

```
docker ps
```

Access the application:

Frontend:

```
http://SERVER_IP:3000
```

Backend API:

```
http://SERVER_IP:3001
```

---

# 6. Screenshots

## Docker Hub Repository

*(Insert screenshot showing pushed images)*

Example:

* whiteobah/bootcamp-linker-frontend
* whiteobah/bootcamp-linker-backend

---

## Application Running in Browser

*(Insert screenshot showing the frontend application running)*

---

## Docker Containers Running

Command used:

```
docker ps
```

*(Insert screenshot showing running containers)*

Example containers:

* linker-frontend
* linker-backend
* linker-postgres

---

# Conclusion

The DevOps Bootcamp Linker application was successfully containerized using Docker. Optimized multi-stage Dockerfiles were created for both frontend and backend applications, images were pushed to Docker Hub, and Docker Compose was used to orchestrate the full application stack including PostgreSQL.

This setup allows the application to be deployed and run consistently across different environments using containers.
