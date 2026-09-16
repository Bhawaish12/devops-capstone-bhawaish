# DevOps Capstone — Bhawaish

A simple static website served by Nginx inside a Docker container.

This project demonstrates Linux, Git, GitHub, Docker,
GitHub Actions CI, and Jenkins deployment.

## Requirements

- Git
- Docker installed and running
- GitHub repository with Actions enabled
- Jenkins with Git and Pipeline plugins
- A Jenkins execution node with Docker access and curl
- The node label docker if required by the Jenkinsfile

## Run locally

From the project directory:

```bash
docker build -t devops-capstone:1.0 .
docker run -d --name devops-capstone-web -p 8080:80 devops-capstone:1.0
```

Open http://localhost:8080 in your browser.

Port 8080 and the container name must be available.
If the project container is already running, use the existing website.

Jenkins uses port 8081 in this local setup.

## Deployment script

```bash
./deploy.sh
```

The script prints: Deployment started...

Container deployment is handled by the Jenkinsfile.

## Continuous integration

The workflow at .github/workflows/ci.yml checks out the code
and builds the Docker image on every push to main.

## Jenkins deployment

The Jenkins job is named capstone-pipeline.

It uses Pipeline script from SCM with these settings:

- Repository: https://github.com/Bhawaish12/devops-capstone-bhawaish.git
- Branch: */main
- Script path: Jenkinsfile

The pipeline runs three stages:

1. Checkout: retrieve the repository code.
2. Build: build the Docker image.
3. Deploy: run the website container.

Start the pipeline using Build Now.

GitHub Actions and Jenkins run independently.

## Stop and remove the website container

Run these commands only when you want to stop the website:

```bash
docker stop devops-capstone-web
docker rm devops-capstone-web
```

## Author

Bhawaish
