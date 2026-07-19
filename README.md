# CodeCollab

A real-time collaborative code editor built with React, Node.js, and Socket.io — containerized with Docker and deployed on AWS ECS.

# Deployment Link-http://docker-aws-yt-alb-768859162.ap-northeast-1.elb.amazonaws.com/
---

## What It Does

CodeCollab lets multiple users edit code together in real time inside a shared room. Changes sync instantly across all connected clients using WebSockets and Yjs (a CRDT library for conflict-free collaborative editing). The app runs behind a single domain — frontend and backend served together — and scales horizontally via Docker containers on AWS.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React.js + Monaco Editor |
| Backend | Node.js + Express.js |
| Real-Time Sync | Socket.io (WebSockets) + Yjs |
| Containerization | Docker (multi-stage builds) |
| Container Registry | AWS ECR |
| Deployment | AWS ECS (Elastic Container Service) |

---

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) v18+
- [Docker](https://docs.docker.com/get-docker/) installed and running

### Run Locally (Without Docker)

```bash
# Clone the repo
git clone https://github.com/ankurdotio/docker-aws
cd docker-aws

# Install and start the backend
cd server
npm install
npm run dev

# In a separate terminal, install and start the frontend
cd ../client
npm install
npm run dev
```

### Run With Docker

```bash
# Build the Docker image
docker build -t codecollab .

# Run the container
docker run -p 3000:3000 codecollab
```

The app will be available at `http://localhost:3000`.

---

## Project Structure

```
docker-aws/
├── client/          # React frontend with Monaco Editor
│   └── src/
├── server/          # Node.js + Express + Socket.io backend
│   └── index.js
├── Dockerfile       # Multi-stage build for production
└── README.md
```

---

## How Collaboration Works

1. A user opens the app and joins (or creates) a room.
2. The frontend connects to the backend over a WebSocket.
3. Keystrokes are encoded as Yjs operations and broadcast to all peers in the room.
4. Each client merges incoming operations using Yjs's CRDT algorithm — no conflicts, no overwrites.
5. A live presence list shows who else is in the room.

---

## Docker & Deployment

This project uses a **multi-stage Dockerfile** to keep the production image lean:

- **Stage 1 (build):** installs dependencies and compiles the React app.
- **Stage 2 (serve):** copies only the build output and production server code into a minimal Node image.

### Deploy to AWS

```bash
# Authenticate Docker with ECR
aws ecr get-login-password --region <region> | \
  docker login --username AWS --password-stdin <account-id>.dkr.ecr.<region>.amazonaws.com

# Tag and push
docker tag codecollab:latest <ecr-repo-uri>:latest
docker push <ecr-repo-uri>:latest
```
