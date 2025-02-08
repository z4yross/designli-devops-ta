# Designli DevOps TA

## Overview

This repository contains the code for the Technical Test for Designli DevOps Lead position.

## Requirements

1. Create a Docker container with a `Linux server` (Ubuntu or Alpine preferred) and install `Node.js`.
2. Create a script that sets up a remote `Git` repository on the server, including a `post-receive` hook that allows automatic deployment when code is pushed.
3. Create a simple React application displaying **Hello `XXX`**, where the value of `XXX` is read from an `.env` file.
4. Push your `React` application to the remote repository on the server and verify that it is automatically deployed and running.
5. Extend the previous setup by configuring an `Nginx `reverse proxy to serve the React application.
6. Expose the reverse proxy publicly using `ngrok`.
7. Ensure that the deployment process remains automated, meaning that any push to the remote repository triggers a deployment without manual intervention.

## Solution

A docker-compose file is provided exposing the following services:

1. **Git Server**: A simple Git server and NodeJS build server using `node:alpine` image.
2. **Nginx Server**: An Nginx server using `nginx:alpine` image.
3. **Ngrok**: A tunneling service using `ngrok/ngrok` image.

### Git Server

The Git server is a simple NodeJS server that listens for incoming Git pushes and deploys the React application to the Nginx server. This Service builds the image located in `server/dockerfile`.

The `post-receive` hook is located in `server/post-receive`. This script is executed when a push is made to the Git server. The script checks that the pushed branch is `main` and then proceeds to install the dependencies and build the React application, placing the build files in the shared volume with the Nginx server.

> [!NOTE]
> The environment variable GIT_PASSWORD must be set to use the remote Git repository.

### Nginx Server

The Nginx server is a simple Nginx server that serves the React application. This Service exposes the static files located in the shaded volume. The Nginx configuration is located in `nginx/nginx.conf`.

### Ngrok

The Ngrok service is a tunneling service that exposes the Nginx server to the public. This Service uses the `ngrok/ngrok` image and exposes the Nginx server on port `80`.

The tunnel URL can be obtained in the `ngrok` dashboard. Or by visiting the Ngrok service URL `http://localhost:4040/status`.

> [!NOTE]
> The environment variable NGROK_AUTH_TOKEN must be set to use the Ngrok service.

### Runnig the project

To run the project, rename or copy the `.env.example` file to `.env` and set the `NGROK_AUTH_TOKEN` and `GIT_PASSWORD` variables. 

```.env
NGROK_AUTH_TOKEN=your_ngrok_auth_token
GIT_PASSWORD=your_git_password
```

Then run the following command:

```bash
docker compose up
```

Once the services are up and running, you can now initialize the Git repository and push the React application to the Git server.

```bash
git init
git branch -M main
git add .
git commit -m "test commit"
git remote add origin ssh://git@localhost:2222/var/repo/site.git
git push origin main
```

> [!NOTE]
> The repository is initialized with a password, so every push will require for you to enter the password.

Once the push is made, the React application will be automatically deployed to the Nginx server and exposed publicly using Ngrok. you can access the application locally using the ngnix service URL `http://localhost:8080` or the Ngrok tunnel URL.

After checking the application, you can make changes to the React application and push them to the Git server. The changes will be automatically deployed to the Nginx server.

> [!CAUTION]
> The Ngnix server has a cache enabled, so you may need to do a hard refresh to see the changes `Ctrl + Shift + R`.


## Conclusion

This project demonstrates a simple CI/CD pipeline using Docker containers. The pipeline is triggered by a Git push and automatically deploys the React application to the Nginx server. The Nginx server is then exposed to the public using Ngrok. The pipeline is fully automated and requires no manual intervention.




