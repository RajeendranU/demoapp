# Flask App Deployment & CI/CD Guide

This guide explains how to deploy a Python Flask application and set up CI/CD pipelines.

## 📋 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Local Development](#local-development)
3. [Deployment Options](#deployment-options)
4. [CI/CD Setup](#cicd-setup)
5. [Step-by-Step Deployment](#step-by-step-deployment)

---

## Prerequisites

- Python 3.11+ installed
- Git installed
- Docker installed (for containerized deployment)
- GitHub/GitLab account (for CI/CD)
- Cloud platform account (AWS, Azure, GCP, Heroku, etc.)

---

## Local Development

### 1. Create Virtual Environment

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Run the Application

```bash
python main.py
```

The app will be available at `http://localhost:5000`

---

## Deployment Options

### Option 1: Docker Deployment

#### Build Docker Image

```bash
docker build -t flask-app .
```

#### Run Container

```bash
docker run -p 5000:5000 flask-app
```

#### Push to Docker Hub

```bash
docker tag flask-app your-username/flask-app:latest
docker push your-username/flask-app:latest
```

### Option 2: Cloud Platform Deployment

#### Heroku

1. Install Heroku CLI
2. Login: `heroku login`
3. Create app: `heroku create your-app-name`
4. Deploy: `git push heroku main`
5. Add Procfile:
   ```
   web: gunicorn main:app
   ```

#### AWS Elastic Beanstalk

1. Install EB CLI: `pip install awsebcli`
2. Initialize: `eb init -p python-3.11 flask-app`
3. Create environment: `eb create flask-env`
4. Deploy: `eb deploy`

#### Azure App Service

1. Install Azure CLI
2. Create resource group: `az group create --name myResourceGroup --location eastus`
3. Create app service plan: `az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1`
4. Create web app: `az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name myFlaskApp --runtime "PYTHON:3.11"`
5. Deploy: `az webapp up --resource-group myResourceGroup --name myFlaskApp`

#### Google Cloud Run

1. Install gcloud CLI
2. Build container: `gcloud builds submit --tag gcr.io/PROJECT-ID/flask-app`
3. Deploy: `gcloud run deploy flask-app --image gcr.io/PROJECT-ID/flask-app --platform managed`

---

## CI/CD Setup

### GitHub Actions (Already Configured)

The `.github/workflows/deploy.yml` file contains a complete CI/CD pipeline that:

1. **Tests** - Runs tests on every push/PR
2. **Lints** - Checks code quality
3. **Builds** - Creates Docker image
4. **Deploys** - Deploys to production (on main branch)

### GitLab CI/CD

Create `.gitlab-ci.yml`:

```yaml
stages:
  - test
  - build
  - deploy

test:
  stage: test
  image: python:3.11
  script:
    - pip install -r requirements.txt
    - pytest tests/

build:
  stage: build
  script:
    - docker build -t flask-app:$CI_COMMIT_SHA .
    - docker push flask-app:$CI_COMMIT_SHA

deploy:
  stage: deploy
  script:
    - echo "Deploy to production"
```

### Jenkins Pipeline

Create `Jenkinsfile`:

```groovy
pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh 'pip install -r requirements.txt'
                sh 'pytest tests/'
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t flask-app .'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker push flask-app'
            }
        }
    }
}
```

---

## Step-by-Step Deployment

### Step 1: Prepare Your Application

✅ Ensure your Flask app is production-ready:
- Set `debug=False` in production
- Use environment variables for configuration
- Add health check endpoints
- Use a production WSGI server (Gunicorn)

### Step 2: Containerize (Optional but Recommended)

✅ Create `Dockerfile` (already included)
✅ Create `.dockerignore` (already included)
✅ Test locally: `docker build -t flask-app . && docker run -p 5000:5000 flask-app`

### Step 3: Set Up CI/CD Pipeline

✅ Choose your CI/CD platform:
- **GitHub Actions**: Already configured in `.github/workflows/deploy.yml`
- **GitLab CI**: Create `.gitlab-ci.yml`
- **Jenkins**: Create `Jenkinsfile`
- **CircleCI**: Create `.circleci/config.yml`

### Step 4: Configure Secrets

For GitHub Actions, add secrets in repository settings:
- `DOCKER_USERNAME` - Docker Hub username
- `DOCKER_PASSWORD` - Docker Hub password
- `SSH_PRIVATE_KEY` - For SSH deployment (if needed)
- `SERVER_HOST` - Production server address

### Step 5: Set Up Production Environment

#### Option A: Docker on VPS

```bash
# On your server
docker pull your-username/flask-app:latest
docker run -d -p 80:5000 --name flask-app --restart unless-stopped your-username/flask-app:latest
```

#### Option B: Cloud Platform

Follow the cloud platform-specific instructions above.

### Step 6: Configure Domain & SSL

- Point your domain to server IP
- Set up SSL certificate (Let's Encrypt with Certbot)
- Configure reverse proxy (Nginx) if needed

### Step 7: Monitor & Maintain

- Set up logging and monitoring
- Configure alerts
- Regular backups
- Update dependencies regularly

---

## Environment Variables

Create a `.env` file for local development:

```env
FLASK_ENV=development
FLASK_DEBUG=True
PORT=5000
```

For production, set these in your deployment platform.

---

## Testing

Run tests locally:

```bash
pip install pytest pytest-cov
pytest tests/
```

---

## Troubleshooting

### Common Issues

1. **Port already in use**: Change port in `main.py` or kill process using port
2. **Docker build fails**: Check Dockerfile syntax and dependencies
3. **CI/CD fails**: Check logs in GitHub Actions/GitLab CI
4. **Deployment fails**: Verify credentials and server access

---

## Best Practices

✅ Use environment variables for configuration
✅ Never commit secrets to repository
✅ Use production WSGI server (Gunicorn, uWSGI)
✅ Set up proper logging
✅ Implement health checks
✅ Use HTTPS in production
✅ Set up monitoring and alerts
✅ Regular security updates
✅ Use container orchestration (Kubernetes) for scale

---

## Next Steps

1. Add more endpoints to your Flask app
2. Set up database (PostgreSQL, MongoDB)
3. Add authentication (JWT, OAuth)
4. Implement caching (Redis)
5. Set up load balancing
6. Add monitoring (Prometheus, Grafana)

---

## Resources

- [Flask Documentation](https://flask.palletsprojects.com/)
- [Docker Documentation](https://docs.docker.com/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Gunicorn Documentation](https://gunicorn.org/)

