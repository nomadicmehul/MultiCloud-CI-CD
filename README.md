# CI/CD Project with Docker, AWS, and Google Cloud

This project demonstrates how to set up a **CI/CD pipeline** for a simple Node.js application using **Docker Desktop**, **AWS**, and **Google Cloud**. The pipeline automates building, testing, and deploying the application across multiple platforms.

## Table of Contents
1. [Project Overview](#project-overview)
2. [Prerequisites](#prerequisites)
3. [Local Setup with Docker Desktop](#local-setup-with-docker-desktop)
4. [CI/CD Pipeline with GitHub Actions](#cicd-pipeline-with-github-actions)
5. [Deploying to AWS](#deploying-to-aws)
6. [Deploying to Google Cloud](#deploying-to-google-cloud)
7. [Contributing](#contributing)
8. [License](#license)

---

## Project Overview
This project includes:
- A simple **Node.js application** that serves a "Hello, World!" message.
- A **Dockerfile** to containerize the application.
- A **GitHub Actions workflow** to automate the CI/CD process.
- Guides for deploying the application to **AWS** and **Google Cloud**.

---

## Prerequisites
Before you begin, ensure you have the following:

1. **Docker Desktop**: Installed and running on your local machine.
   - Download: [Docker Desktop](https://www.docker.com/products/docker-desktop)
2. **GitHub Account**: For version control and CI/CD pipeline.
   - Sign up: [GitHub](https://github.com/)
3. **Docker Hub Account**: For storing Docker images.
   - Sign up: [Docker Hub](https://hub.docker.com/)
4. **AWS Account**: For deploying to AWS.
   - Sign up: [AWS](https://aws.amazon.com/)
5. **Google Cloud Account**: For deploying to Google Cloud.
   - Sign up: [Google Cloud](https://cloud.google.com/)
6. **Node.js**: Installed on your local machine (optional, for local testing).
   - Download: [Node.js](https://nodejs.org/)

---

## Local Setup with Docker Desktop

### Step 1: Clone the Repository
```bash
git clone https://github.com/nomadicmehul/my-ci-cd-project.git
cd my-ci-cd-project
```

### Step 2: Build the Docker Image
```bash
docker build -t my-ci-cd-project .
```

### Step 3: Run the Docker Container
```bash
docker run -p 3000:3000 my-ci-cd-project
```

### Step 4: Test the Application
Open your browser and navigate to `http://localhost:3000`. You should see "Hello, World!".

---

## CI/CD Pipeline with GitHub Actions

### Step 1: Set Up GitHub Secrets
1. Go to your GitHub repository.
2. Navigate to **Settings** > **Secrets and variables** > **Actions**.
3. Add the following secrets:
   - `DOCKER_HUB_USERNAME`: Your Docker Hub username.
   - `DOCKER_HUB_TOKEN`: Your Docker Hub access token.

### Step 2: Push Changes to GitHub
Push your code to GitHub to trigger the CI/CD pipeline:
```bash
git add .
git commit -m "Set up CI/CD pipeline"
git push origin main
```

### Step 3: Monitor the Pipeline
1. Go to the **Actions** tab in your GitHub repository.
2. Monitor the workflow to ensure it builds and pushes the Docker image to Docker Hub.

---

## Deploying to AWS

### Step 1: Set Up AWS ECR
1. Log in to the AWS Management Console.
2. Go to **Elastic Container Registry (ECR)** and create a new repository.
3. Authenticate Docker to your ECR registry:
   ```bash
   aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com
   ```

### Step 2: Update GitHub Actions Workflow
Update the `.github/workflows/ci-cd.yml` file to push the Docker image to AWS ECR:
```yaml
- name: Login to AWS ECR
  id: login-ecr
  uses: aws-actions/amazon-ecr-login@v1

- name: Build, tag, and push image to Amazon ECR
  env:
    ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
    ECR_REPOSITORY: my-ci-cd-project
    IMAGE_TAG: latest
  run: |
    docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
    docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
```

### Step 3: Deploy to AWS ECS
1. Set up an **Elastic Container Service (ECS)** cluster.
2. Create a task definition and service to deploy the Docker image from ECR.

---

## Deploying to Google Cloud

### Step 1: Set Up Google Cloud Container Registry
1. Log in to the Google Cloud Console.
2. Enable the **Container Registry API**.
3. Authenticate Docker to Google Cloud:
   ```bash
   gcloud auth configure-docker
   ```

### Step 2: Update GitHub Actions Workflow
Update the `.github/workflows/ci-cd.yml` file to push the Docker image to Google Cloud Container Registry:
```yaml
- name: Login to Google Cloud
  uses: google-github-actions/auth@v1
  with:
    credentials_json: ${{ secrets.GCP_CREDENTIALS }}

- name: Build, tag, and push image to Google Cloud
  run: |
    docker build -t gcr.io/<project-id>/my-ci-cd-project:latest .
    docker push gcr.io/<project-id>/my-ci-cd-project:latest
```

### Step 3: Deploy to Google Cloud Run
1. Go to **Cloud Run** in the Google Cloud Console.
2. Create a new service and deploy the Docker image from Container Registry.

---

## Contributing
Contributions are welcome! If you'd like to contribute to this project, please follow these steps:
1. Fork the repository.
2. Create a new branch (`git checkout -b feature/YourFeature`).
3. Commit your changes (`git commit -m 'Add some feature'`).
4. Push to the branch (`git push origin feature/YourFeature`).
5. Open a pull request.

---

## License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## Notes
- Replace placeholders like `<region>`, `<aws_account_id>`, and `<project-id>` with your actual values.
- Ensure all secrets (e.g., `DOCKER_HUB_TOKEN`, `GCP_CREDENTIALS`) are correctly set up in GitHub Secrets.
- For detailed AWS and Google Cloud setup guides, refer to their official documentation.
