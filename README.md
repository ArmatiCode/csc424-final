# CSC 424 Final Exam Part 2 
# Containerized Web App

A containerized React + Vite frontend and .NET backend, fronted by an Nginx reverse proxy, deployed via GitHub Actions to a (mock) QA server.

## Dev Ops Setup

### Running locally

Make sure Docker is installed and running, from the repo root do: 
```docker compose up --build -d```

This builds and starts all three services in the background. It may take a second.

### Ports & Urls

In this case, only one port is exposed to the host, port 80, served via the nginx proxy

`http://localhost` will load the frontend
`http:localhost/api/ping` returns a response from the .net backend

The setup consists of not exposing the frontend or backend containers to the host directly, the nginx proxy will have the traffic come in through the proxy.


### Services

The following services are defined in this stack:

- frontend: react + vite app built with npm run build and served as static files via nginx instance built via a dockerfile so the image contains no node on runtime OR source code
- backend: .NET 10 api exposing routes under ./api/*
- nginx: reverse proxy; routes '/' to frontend and '/api/' to backend. (single entry point)


### CD/CI Pipeline
The pipeline is defined in `.github/workflows/deploy-qa.yml` and is triggered by a push to main

On each push it will...

- check out the code on a self hosted runner (i didnt have a server) installed on the qa server
- logins into github container registry using the auto-generated tokens
- builds and pushes three docker images (front, back and nginx) to ghcr
- pulls latest images on qa server using the compose qa yml 
- Runs `docker compose -f docker-compose.qa.yml up -d` to redeploy

Credentials should not be exposed and held in github secrets.


