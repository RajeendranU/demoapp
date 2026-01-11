# Complete Guide: Flask App Deployment with CI/CD to Azure

This comprehensive guide walks you through creating a Python Flask application, containerizing it with Docker, setting up CI/CD with GitHub Actions, and deploying to Azure App Service.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Step 1: Create Flask Application](#step-1-create-flask-application)
3. [Step 2: Set Up Project Structure](#step-2-set-up-project-structure)
4. [Step 3: Containerize with Docker](#step-3-containerize-with-docker)
5. [Step 4: Set Up GitHub Repository](#step-4-set-up-github-repository)
6. [Step 5: Configure GitHub Actions CI/CD](#step-5-configure-github-actions-cicd)
7. [Step 6: Deploy to Azure App Service](#step-6-deploy-to-azure-app-service)
8. [Step 7: Set Up Continuous Deployment](#step-7-set-up-continuous-deployment)
9. [Step 8: Test the Deployment](#step-8-test-the-deployment)
10. [Troubleshooting](#troubleshooting)
11. [Next Steps](#next-steps)

---

## Prerequisites

Before starting, ensure you have the following installed:

- **Python 3.11+** - [Download Python](https://www.python.org/downloads/)
- **Git** - [Download Git](https://git-scm.com/downloads)
- **Docker Desktop** - [Download Docker](https://www.docker.com/products/docker-desktop/)
- **Azure CLI** - [Install Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
- **GitHub Account** - [Sign up for GitHub](https://github.com/)
- **Azure Account** - [Sign up for Azure](https://azure.microsoft.com/free/)

---

## Step 1: Create Flask Application

### 1.1 Create Project Directory

```bash
mkdir flask-app
cd flask-app
```

### 1.2 Create Virtual Environment

**Windows (PowerShell):**
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**Windows (CMD):**
```cmd
python -m venv venv
venv\Scripts\activate
```

**Linux/Mac:**
```bash
python3 -m venv venv
source venv/bin/activate
```

### 1.3 Create Flask Application

Create a file named `main.py`:

```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def home():
    return jsonify({
        'message': 'Hello, World!',
        'status': 'success'
    })

@app.route('/health')
def health():
    return jsonify({'status': 'healthy'}), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=False)
```

### 1.4 Create Requirements File

Create `requirements.txt`:

```
Flask==3.0.0
gunicorn==21.2.0
```

### 1.5 Install Dependencies

```bash
pip install -r requirements.txt
```

### 1.6 Test Locally

```bash
python main.py
```

Visit `http://localhost:5000` in your browser. You should see:
```json
{"message":"Hello, World!","status":"success"}
```

Visit `http://localhost:5000/health` to see:
```json
{"status":"healthy"}
```

Press `Ctrl+C` to stop the server.

---

## Step 2: Set Up Project Structure

### 2.1 Create Additional Files

**Create `.gitignore`:**

```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
ENV/
.venv

# Flask
instance/
.webassets-cache

# Environment variables
.env
.env.local

# IDE
.vscode/
.idea/
*.swp
*.swo

# Testing
.pytest_cache/
.coverage
htmlcov/

# Logs
*.log

# OS
.DS_Store
Thumbs.db
```

**Create `Procfile` (for Heroku compatibility):**

```
web: gunicorn main:app
```

**Create `tests/test_app.py` (optional but recommended):**

```python
import pytest
from main import app

@pytest.fixture
def client():
    app.config['TESTING'] = True
    with app.test_client() as client:
        yield client

def test_home_endpoint(client):
    """Test the home endpoint"""
    response = client.get('/')
    assert response.status_code == 200
    data = response.get_json()
    assert data['status'] == 'success'
    assert 'message' in data

def test_health_endpoint(client):
    """Test the health check endpoint"""
    response = client.get('/health')
    assert response.status_code == 200
    data = response.get_json()
    assert data['status'] == 'healthy'
```

---

## Step 3: Containerize with Docker

### 3.1 Create Dockerfile

Create `Dockerfile`:

```dockerfile
# Use Python 3.11 slim image
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy requirements file
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY main.py .

# Expose port 5000
EXPOSE 5000

# Use gunicorn to run the Flask app
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "4", "main:app"]
```

### 3.2 Create .dockerignore

Create `.dockerignore`:

```
__pycache__
*.pyc
*.pyo
*.pyd
.Python
env/
venv/
.venv/
.git
.gitignore
README.md
.env
*.log
```

### 3.3 Build Docker Image

```bash
docker build -t flask-app .
```

### 3.4 Test Docker Container

**Run in foreground:**
```bash
docker run -p 5000:5000 flask-app
```

**Run in background:**
```bash
docker run -d -p 5000:5000 --name flask-app flask-app
```

**Check if running:**
```bash
docker ps
```

**View logs:**
```bash
docker logs flask-app
```

**Stop container:**
```bash
docker stop flask-app
docker rm flask-app
```

Test at `http://localhost:5000` - should work the same as before.

---

## Step 4: Set Up GitHub Repository

### 4.1 Initialize Git Repository

```bash
git init
```

### 4.2 Configure Git (First Time Only)

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### 4.3 Add Files and Commit

```bash
git add .
git commit -m "Initial Flask app with Docker setup"
```

### 4.4 Create GitHub Repository

1. Go to [GitHub](https://github.com/)
2. Click the **+** icon → **New repository**
3. Name: `flask-app` (or your preferred name)
4. **Don't** initialize with README
5. Click **Create repository**

### 4.5 Connect and Push to GitHub

```bash
git remote add origin https://github.com/YOUR_USERNAME/flask-app.git
git branch -M main
git push -u origin main
```

Replace `YOUR_USERNAME` with your GitHub username.

---

## Step 5: Set Up GitHub Repository for CI/CD

**Note:** The GitHub Actions workflow will be automatically created by Azure when you set up continuous deployment in Step 7. You don't need to create it manually.

For now, just ensure your code is pushed to GitHub:

```bash
git add .
git commit -m "Initial Flask app setup"
git push
```

---

## Step 6: Deploy to Azure App Service

### 6.1 Install Azure CLI

If not already installed:
- **Windows:** Download from [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows)
- **Mac:** `brew install azure-cli`
- **Linux:** Follow [Azure CLI installation guide](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-linux)

### 6.2 Login to Azure

```bash
az login
```

This will open a browser window. Sign in with your Azure account.

### 6.3 Create Resource Group

```bash
az group create --name flask-rg --location eastus
```

Replace `flask-rg` with your preferred name and `eastus` with your preferred location.

### 6.4 Create App Service Plan

```bash
az appservice plan create --name flask-plan --resource-group flask-rg --sku B1 --is-linux
```

- `flask-plan`: Your plan name
- `flask-rg`: Your resource group name
- `B1`: Basic tier (you can use `F1` for free tier)

### 6.5 Create Web App

```bash
az webapp create --resource-group flask-rg --plan flask-plan --name YOUR_APP_NAME --runtime "PYTHON:3.11"
```

Replace `YOUR_APP_NAME` with a globally unique name (e.g., `flask-app-12345`).

**Note:** The app name must be globally unique. If it's taken, try a different name.

### 6.6 Deploy Using Azure CLI

**Option A: Deploy from Local Directory**

```bash
az webapp up --resource-group flask-rg --name YOUR_APP_NAME
```

**Option B: Deploy Using VS Code Azure Extension**

1. Install the **Azure App Service** extension in VS Code
2. Sign in to Azure
3. Right-click your App Service → **Deploy to Web App...**
4. Select your project folder

### 6.7 Configure Startup Command

After deployment, configure the startup command:

1. Go to [Azure Portal](https://portal.azure.com)
2. Navigate to your App Service
3. Go to **Configuration** → **General settings**
4. Find **Startup Command**
5. Enter: `gunicorn --bind=0.0.0.0:8000 main:app`
6. Click **Save**

**Note:** Azure App Service uses port 8000 (not 5000).

### 6.8 Verify Deployment

Visit your app URL:
```
https://YOUR_APP_NAME.azurewebsites.net/
```

You should see:
```json
{"message":"Hello, World!","status":"success"}
```

---

## Step 7: Set Up Continuous Deployment

### 7.1 Create Azure Service Principal

This allows GitHub Actions to deploy to Azure.

```bash
# Get your subscription ID
az account show --query id -o tsv

# Get your resource group name
az group list --query "[].name" -o tsv

# Create service principal (replace with your values)
az ad sp create-for-rbac --name "github-actions-flask-deploy" \
  --role contributor \
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> \
  --sdk-auth
```

**Example:**
```bash
az ad sp create-for-rbac --name "github-actions-flask-deploy" \
  --role contributor \
  --scopes /subscriptions/1cba9582-bc46-431c-8294-547f3afd860d/resourceGroups/flask-rg/providers/Microsoft.Web/sites/flask-app-12345 \
  --sdk-auth
```

This will output JSON credentials. **Copy the entire JSON output.**

### 7.2 Add Secret to GitHub

1. Go to your GitHub repository
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `AZURE_CREDENTIALS`
5. Value: Paste the entire JSON from step 7.1
6. Click **Add secret**

### 7.3 Set Up Continuous Deployment in Azure Portal

Azure will automatically create the GitHub Actions workflow file for you:

1. Go to [Azure Portal](https://portal.azure.com)
2. Navigate to your App Service
3. Click **Deployment Center** (in the left menu)
4. In the **Settings** tab:
   - **Source**: Select **GitHub**
   - **Organization**: Select your GitHub organization/user
   - **Repository**: Select your repository (e.g., `flask-app`)
   - **Branch**: Select `main`
   - **Build provider**: Select **GitHub Actions**
   - Click **Save**

Azure will create a workflow file (typically `.github/workflows/main_YOUR_APP_NAME.yml`) in your repository automatically.

### 7.4 Pull the Azure-Created Workflow

Pull the workflow file that Azure created:

```bash
git pull origin main
```

You should now see a workflow file like `.github/workflows/main_flask-app1.yml` (the exact name depends on your app name).

### 7.5 Update Workflow with Service Principal

Edit the Azure-created workflow file (e.g., `.github/workflows/main_flask-app1.yml`) and update it:

```yaml
# Docs for the Azure Web Apps Deploy action: https://github.com/Azure/webapps-deploy
# More GitHub Actions for Azure: https://github.com/Azure/actions
# More info on Python, GitHub Actions, and Azure App Service: https://aka.ms/python-webapps-actions

name: Build and deploy Python app to Azure Web App - flask-app1

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python version
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Create and Start virtual environment and Install dependencies
        run: |
          python -m venv antenv
          source antenv/bin/activate
          pip install -r requirements.txt
                
      - name: Upload artifact for deployment jobs
        uses: actions/upload-artifact@v4
        with:
          name: python-app
          path: |
            .
            !antenv/

  deploy:
    runs-on: ubuntu-latest
    needs: build

    steps:
      - name: Download artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: python-app
      
      - name: Login to Azure
        uses: azure/login@v2
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: 'Deploy to Azure Web App'
        uses: azure/webapps-deploy@v3
        id: deploy-to-webapp
        with:
          app-name: 'YOUR_APP_NAME'
          slot-name: 'Production'
          startup-command: 'gunicorn --bind=0.0.0.0:8000 main:app'
```

**Important:** Replace `YOUR_APP_NAME` with your actual Azure App Service name.

**Key changes to make:**
1. Replace the OIDC authentication (lines with `client-id`, `tenant-id`, `subscription-id`) with:
   ```yaml
   - name: Login to Azure
     uses: azure/login@v2
     with:
       creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```
2. Remove the `permissions` block from the deploy job (not needed with service principal)
3. Ensure `startup-command: 'gunicorn --bind=0.0.0.0:8000 main:app'` is included
4. Verify the `app-name` matches your Azure App Service name

**Complete updated deploy section should look like:**

```yaml
  deploy:
    runs-on: ubuntu-latest
    needs: build

    steps:
      - name: Download artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: python-app
      
      - name: Login to Azure
        uses: azure/login@v2
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: 'Deploy to Azure Web App'
        uses: azure/webapps-deploy@v3
        id: deploy-to-webapp
        with:
          app-name: 'YOUR_APP_NAME'
          slot-name: 'Production'
          startup-command: 'gunicorn --bind=0.0.0.0:8000 main:app'
```

**Important:** Replace `YOUR_APP_NAME` with your actual Azure App Service name.

### 7.6 Commit and Push Updated Workflow

```bash
git add .github/workflows/
git commit -m "Update Azure deployment workflow to use service principal"
git push
```

### 7.7 Verify Continuous Deployment

1. Go to GitHub → **Actions** tab
2. You should see the workflow running
3. Wait for it to complete
4. Visit your Azure app URL to verify the deployment

---

## Step 8: Test the Deployment

### 8.1 Make a Test Change

Update `main.py`:

```python
@app.route('/')
def home():
    return jsonify({
        'message': 'Hello from Azure! Automatic deployment works!',
        'status': 'success'
    })
```

### 8.2 Commit and Push

```bash
git add main.py
git commit -m "Test automatic deployment"
git push
```

### 8.3 Monitor Deployment

1. Go to GitHub → **Actions** tab
2. Watch the workflow run
3. Wait for deployment to complete (1-2 minutes)

### 8.4 Verify Changes

Visit your Azure app URL. You should see the updated message:
```json
{"message":"Hello from Azure! Automatic deployment works!","status":"success"}
```

**Congratulations!** Your CI/CD pipeline is now fully automated! 🎉

---

## Troubleshooting

### Issue: Docker build fails

**Solution:**
- Check Dockerfile syntax
- Ensure all files are in the correct location
- Verify requirements.txt is correct

### Issue: Azure login fails

**Solution:**
```bash
az login --use-device-code
```
Follow the instructions to authenticate.

### Issue: App Service not found

**Solution:**
- Verify the app name is correct
- Check resource group name
- List apps: `az webapp list --query "[].name" -o tsv`

### Issue: GitHub Actions deployment fails

**Solution:**
- Verify `AZURE_CREDENTIALS` secret is set correctly
- Check the JSON format (must be valid JSON)
- Ensure service principal has correct permissions

### Issue: App shows default Azure page

**Solution:**
- Set startup command in Azure Portal:
  - Configuration → General settings → Startup Command
  - Enter: `gunicorn --bind=0.0.0.0:8000 main:app`
  - Save and restart

### Issue: Port errors

**Solution:**
- Azure uses port 8000, not 5000
- Update startup command to use port 8000
- Update gunicorn bind to `0.0.0.0:8000`

---

## Next Steps

### Enhance Your Application

1. **Add Database**
   - PostgreSQL, MySQL, or MongoDB
   - Use SQLAlchemy for ORM

2. **Add Authentication**
   - JWT tokens
   - OAuth integration

3. **Add More Endpoints**
   - RESTful API design
   - Error handling

4. **Add Logging**
   - Application Insights
   - Custom logging

### Improve Deployment

1. **Custom Domain**
   - Configure in Azure Portal
   - Set up DNS records

2. **SSL Certificate**
   - Azure provides free SSL
   - Enable in App Service settings

3. **Environment Variables**
   - Add in Azure Portal → Configuration
   - Use for secrets and configuration

4. **Deployment Slots**
   - Create staging environment
   - Test before production

### Monitoring

1. **Application Insights**
   - Enable in Azure Portal
   - Monitor performance

2. **Log Streaming**
   - View real-time logs
   - Debug issues

---

## Summary

You've successfully:

✅ Created a Flask application  
✅ Containerized with Docker  
✅ Set up GitHub repository  
✅ Configured CI/CD with GitHub Actions  
✅ Deployed to Azure App Service  
✅ Set up continuous deployment  
✅ Tested automatic deployment  

Your Flask app now has a complete CI/CD pipeline that automatically builds, tests, and deploys on every code push!

---

## Resources

- [Flask Documentation](https://flask.palletsprojects.com/)
- [Docker Documentation](https://docs.docker.com/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Azure App Service Documentation](https://docs.microsoft.com/en-us/azure/app-service/)
- [Gunicorn Documentation](https://gunicorn.org/)

---

**Happy Coding! 🚀**

