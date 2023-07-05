### Dockerfile

```Dockerfile
# Stage 1: Build the application
FROM nginx:alpine AS build

WORKDIR /app

# Copy the necessary files
COPY . .

# Stage 2: Serve the application using Nginx
FROM nginx:alpine

# Copy the built application from the previous stage
COPY --from=build /app /usr/share/nginx/html
```

### GitHub Actions Workflow for Building and Pushing Docker Image

Here's the workflow file to build and push the Docker image to the GitHub Container Registry using GitHub Actions:

```yaml
name: Build and Push Docker Image

on:
  push:
    branches:
      - main

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Build and push Docker image
        env:
          DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
          DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
          IMAGE_TAG: ghcr.io/your-username/your-image:latest
        run: |
          echo $DOCKER_PASSWORD | docker login ghcr.io -u $DOCKER_USERNAME --password-stdin
          docker buildx build --platform linux/amd64 -t $IMAGE_TAG --push .
```

Ensure that you replace `your-username` and `your-image` with your GitHub username and desired image name, respectively. Also, make sure you have set up the appropriate Docker username and password secrets in your GitHub repository settings.

### Kubernetes Deployment YAML

Here's the YAML file for deploying the application to Minikube using GitHub Actions:

```yaml
name: Deploy to Minikube using GitHub Actions

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Minikube
        uses: medyagh/setup-minikube@master

      - name: Try the cluster!
        run: kubectl get pods -A

      - name: Build image
        run: |
          export SHELL=/bin/bash
          eval $(minikube -p minikube docker-env)
          docker build -f Dockerfile -t kalpanamamnani/todo_app:latest .
          echo -n "verifying images:"
          docker images

      - name: Deploy to Minikube
        run: kubectl apply -f k8s-deploy.yaml

      - name: Test service URLs
        run: |
          minikube service list
          minikube service todo_app --url
```

