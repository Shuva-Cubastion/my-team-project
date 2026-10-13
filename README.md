# my-team-project

Node.js (Express) + PostgreSQL backend, React frontend, Docker, nginx load balancing and a Jenkins CI/CD pipeline.

## Run with Docker Compose
    cp .env.example .env      # then edit the password
    docker compose up -d --build --scale app=3
    docker compose restart nginx
    curl localhost:8080/

## Jenkins
    cd jenkins && docker build -t my-jenkins:latest .
See `Jenkinsfile` for the pipeline: checkout, npm install, test, docker build, push, deploy, verify.
