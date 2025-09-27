# CI/CD Pipeline with SonarQube + Docker

This project demonstrates a simple Flask application integrated into a CI/CD pipeline using Jenkins, SonarQube, Docker, and GitHub.

## Features
- Flask web app
- Unit test script
- Dockerized build
- SonarQube code quality analysis
- Jenkins pipeline with GitHub webhook
- DockerHub push and EC2 deployment

## Run Locally
```bash
docker build -t ci-cd-flask-app .
docker run -d -p 5000:5000 ci-cd-flask-app
