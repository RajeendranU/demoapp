# Complete Step-by-Step Prompts for Flask Deployment with CI/CD

Follow these prompts in Cursor one by one to deploy a Flask app with products (TV, Mobile, Smart Devices) to Azure with CI/CD.

**This guide uses MCP (Model Context Protocol) GitHub tools to automate GitHub operations!**

**Prompt Pattern:**
- **Regular prompts** (e.g., Prompt 3, 6, 9, 11): Show commands and explain steps
- **Execution prompts** (e.g., Prompt 3a, 6a, 9a, 11a): ⚡ Actually execute the commands
- **MCP prompts** (🚀): Use MCP tools for automated operations

**Workflow:** First see the commands → Then execute them → Then verify results

---

## **MCP GitHub Automation**

This guide leverages MCP GitHub server to automate:
- ✅ Creating GitHub repositories
- ✅ Pushing files directly to GitHub
- ✅ Updating files and committing changes
- ✅ Checking workflow status
- ✅ Monitoring deployments

**Prerequisites for MCP:**
- MCP GitHub server configured in Cursor
- GitHub authentication set up
- Repository access permissions

**Benefits:**
- No manual git commands needed
- Faster workflow - all operations from Cursor
- Automated commits and pushes
- Real-time workflow monitoring

---

## **Step 1: Create Flask Application**

### **Prompt 1: Create Flask App with Products**
```
Create a Flask application file named main.py with the following:
1. Home route (/) that returns a JSON list of products with categories:
   - TV: Samsung 55" Smart TV, LG 65" OLED, Sony 43" 4K
   - Mobile: iPhone 15 Pro, Samsung Galaxy S24, OnePlus 12
   - Smart Devices: Amazon Echo Dot, Google Nest Hub, Apple HomePod
   Each product should have: id, name, category, price, and description
2. Health route (/health) that returns JSON: {"status": "healthy"}
3. Products route (/products) that returns all products
4. Products by category route (/products/<category>) that filters by category (tv, mobile, smart-devices)
Use Flask and jsonify. Set host to 0.0.0.0 and port to 5000.
```

---

## **Step 2: Create All CI/CD Setup Files**

### **Prompt 2: Create All CI/CD Files**
```
Create all the necessary files for CI/CD setup:
1. requirements.txt with Flask==3.0.0 and gunicorn==21.2.0
2. .gitignore file for Python/Flask that excludes:
   - Python cache files (__pycache__, *.pyc, etc.)
   - Virtual environments (venv/, env/, etc.)
   - IDE files (.vscode/, .idea/)
   - Environment variables (.env)
   - Logs (*.log)
   - OS files (.DS_Store, Thumbs.db)
3. Procfile with: web: gunicorn main:app
4. tests/test_app.py with pytest tests for:
   - Home endpoint returns products
   - Health endpoint returns healthy status
   - Products endpoint returns all products
   - Products by category endpoint filters correctly
Create all these files in one go.
```

---

## **Step 3: Test Locally**

### **Prompt 3: Test Flask App Locally**
```
Show me the commands to:
1. Create a Python virtual environment
2. Activate it (Windows PowerShell)
3. Install dependencies from requirements.txt
4. Run the Flask app
5. Test the endpoints: /, /health, /products, /products/tv
```

### **Prompt 3a: Execute Local Testing**
```
Now execute these commands:
1. Create and activate the virtual environment
2. Install dependencies from requirements.txt
3. Run the Flask app
4. Verify it's running on http://localhost:5000
Execute all commands and confirm the app is working.
```

---

## **Step 4: Containerize with Docker**

### **Prompt 4: Create Dockerfile**
```
Create a Dockerfile for the Flask app that:
- Uses Python 3.11 slim image
- Sets working directory to /app
- Copies requirements.txt and installs dependencies
- Copies main.py
- Exposes port 5000
- Runs gunicorn with 4 workers on port 5000
```

### **Prompt 5: Create .dockerignore**
```
Create a .dockerignore file that excludes:
- Python cache files
- Virtual environments
- Git files
- IDE files
- Logs
- Documentation files
```

### **Prompt 6: Build and Test Docker**
```
Show me the Docker commands to:
1. Build the image with tag flask-app
2. Run the container in foreground
3. Test the endpoints in browser
4. Run the container in background
5. View logs
6. Stop and remove the container
```

### **Prompt 6a: Execute Docker Build and Test**
```
Now execute these Docker commands:
1. Build the Docker image with tag flask-app
2. Run the container and test it
3. Verify all endpoints work: /, /health, /products, /products/tv
4. Stop the container when done
Execute all commands and confirm Docker container is working.
```

---

## **Step 5: Set Up GitHub Repository**

### **Prompt 7: Create GitHub Repository and Push Files Using MCP**
```
Using MCP GitHub server, automate the GitHub setup:
1. Create a new GitHub repository named "flask-products-app" (or your preferred name)
2. Create README.md file with project description
3. Push all project files to the repository:
   - main.py
   - requirements.txt
   - .gitignore
   - Procfile
   - Dockerfile
   - .dockerignore
   - tests/test_app.py
4. Commit message: "Initial Flask products app with Docker setup"
5. Push to main branch
Use MCP GitHub tools to create the repository and push all files in one operation.
```

---

## **Step 6: Prepare for CI/CD**

### **Prompt 8: Verify GitHub Setup Using MCP**
```
Using MCP GitHub server, verify the repository setup:
1. List all files in the repository to confirm everything is pushed
2. Check the repository structure
3. Verify the main branch exists
4. Confirm all required files are present (main.py, requirements.txt, Dockerfile, etc.)
Note: The GitHub Actions workflow will be created automatically by Azure in Step 7.
```

---

## **Step 7: Deploy to Azure App Service**

### **Prompt 9: Create Azure Resources**
```
Show me the Azure CLI commands to:
1. Login to Azure
2. Create a resource group named flask-rg in eastus location
3. Create an App Service plan named flask-plan with B1 SKU for Linux
4. Create a web app with Python 3.11 runtime (use a unique name like flask-products-XXXXX)
5. List the created web app to verify
```

### **Prompt 9a: Execute Azure Resource Creation**
```
Now execute these Azure CLI commands:
1. Login to Azure (use az login)
2. Create the resource group flask-rg in eastus
3. Create the App Service plan flask-plan with B1 SKU for Linux
4. Create the web app with a unique name and Python 3.11 runtime
5. Verify all resources were created successfully
Execute all commands and confirm Azure resources are created.
```

### **Prompt 10: Deploy and Configure Azure App**
```
After creating the Azure App Service, I need to:
1. Deploy the app using Azure CLI command: az webapp up
2. Configure the startup command in Azure Portal to: gunicorn --bind=0.0.0.0:8000 main:app
3. Verify the deployment by visiting the app URL
Explain how to do all three steps with exact commands and portal navigation.
```

### **Prompt 10a: Execute Azure Deployment**
```
Now execute the deployment:
1. Run: az webapp up --resource-group flask-rg --name YOUR_APP_NAME
2. Configure startup command in Azure Portal (navigate and set it)
3. Verify deployment by visiting the app URL
4. Test all endpoints: /, /health, /products, /products/tv
Execute the deployment and confirm the app is live on Azure.
```

---

## **Step 8: Set Up Continuous Deployment**

### **Prompt 11: Create Azure Service Principal**
```
Show me the Azure CLI commands to:
1. Get my subscription ID
2. Get my resource group name
3. Get my web app name
4. Create a service principal named "github-actions-flask-deploy" with contributor role scoped to my App Service
The command should output JSON credentials in SDK auth format.
Make sure to use the actual values from steps 1-3 in the command.
```

### **Prompt 11a: Execute Service Principal Creation**
```
Now execute these Azure CLI commands:
1. Get subscription ID using: az account show --query id -o tsv
2. Get resource group name using: az group list --query "[].name" -o tsv
3. Get web app name using: az webapp list --query "[].name" -o tsv
4. Create the service principal with the actual values from steps 1-3
5. Save the JSON output - we'll need it for GitHub secrets
Execute all commands and display the service principal JSON credentials.
```

### **Prompt 12: Add GitHub Secret**
```
I need to add the Azure credentials as a GitHub secret. Explain:
1. Where to go in GitHub (Settings → Secrets and variables → Actions)
2. What to name the secret (AZURE_CREDENTIALS)
3. What value to paste (the entire JSON from service principal)
4. How to verify the secret was added
```

### **Prompt 13: Set Up Azure Deployment Center**
```
Explain how to set up continuous deployment in Azure Portal:
1. Navigate to Deployment Center
2. Configure GitHub as source
3. Select repository and branch (main)
4. Choose GitHub Actions as build provider
5. Save (this will create the workflow file automatically)
6. What to expect after saving
```

### **Prompt 14: Get Azure-Created Workflow Using MCP**
```
Using MCP GitHub server, get the workflow file that Azure created:
1. List files in .github/workflows/ directory
2. Get the workflow file content (typically main_YOUR_APP_NAME.yml)
3. Display the workflow file to see what Azure created
4. Verify the file structure and content
Use MCP GitHub tools to read the file directly from the repository.
```

### **Prompt 15: Update Workflow with Service Principal**
```
Update the Azure-created workflow file to use service principal authentication:
1. Replace OIDC authentication (client-id, tenant-id, subscription-id) with: creds: ${{ secrets.AZURE_CREDENTIALS }}
2. Remove the permissions block from deploy job (not needed with service principal)
3. Ensure startup-command is set to: gunicorn --bind=0.0.0.0:8000 main:app
4. Verify app-name matches the Azure App Service name
Show me the complete updated deploy section with all changes highlighted.
```

### **Prompt 16: Update and Push Workflow Using MCP**
```
Using MCP GitHub server, update and push the workflow file:
1. Update the workflow file (.github/workflows/main_YOUR_APP_NAME.yml) with the service principal changes
2. Commit with message "Update Azure deployment workflow to use service principal"
3. Push to main branch
4. Verify the file was updated in GitHub
Use MCP GitHub tools to update the file and push in one operation.
```

### **Prompt 17: Verify Continuous Deployment Using MCP**
```
Using MCP GitHub server, verify continuous deployment:
1. List recent workflow runs to see if the deployment triggered
2. Get the status of the latest workflow run
3. Check if it succeeded or failed
4. Get workflow run details and any error messages
5. Verify the workflow file is correct
6. Visit Azure app URL to verify deployment
Use MCP GitHub tools to check workflow status programmatically.
```

---

## **Step 9: Test the Deployment**

### **Prompt 18: Test Automatic Deployment Using MCP**
```
Using MCP GitHub server, test automatic deployment:
1. Update main.py to add a new product (e.g., "iPad Pro" to Smart Devices category)
2. Update the file in GitHub repository using MCP
3. Commit with message "Test automatic deployment - add new product"
4. Push to main branch
5. Use MCP to check workflow run status
6. Monitor the deployment execution
7. Verify the new product appears on the Azure app
Use MCP GitHub tools to update, commit, and push in one operation, then monitor the workflow.
```

### **Prompt 19: Verify Deployment Success**
```
Explain how to verify the automatic deployment worked:
1. Check GitHub Actions tab for successful workflow run
2. Wait for deployment to complete (1-2 minutes)
3. Visit Azure app URL and test endpoints:
   - / (should show all products)
   - /health (should return healthy)
   - /products (should return all products)
   - /products/tv (should return only TV products)
4. Verify the new product appears in the response
```

---

## **Troubleshooting Prompts**

### **Prompt 20: Docker Build Issues**
```
If Docker build fails, what should I check?
1. Dockerfile syntax errors
2. File locations
3. requirements.txt format
4. How to see detailed error messages
```

### **Prompt 21: Azure Login Issues**
```
If Azure login fails, what command should I use instead?
Show me the alternative login method with device code.
```

### **Prompt 22: App Shows Default Azure Page**
```
If the app shows default Azure page instead of my Flask app, what startup command should I set?
Explain:
1. Where to set it (Azure Portal → Configuration → General settings)
2. Exact command to enter
3. How to save and restart
```

### **Prompt 23: GitHub Actions Deployment Fails**
```
If GitHub Actions deployment fails, what should I verify?
1. AZURE_CREDENTIALS secret format
2. Service principal permissions
3. App name in workflow file
4. How to check workflow logs
5. Common error messages and solutions
```

### **Prompt 24: Port Errors**
```
If there are port errors, explain:
1. Why Azure uses port 8000, not 5000
2. How to update startup command
3. How to update gunicorn bind address
4. Where to verify port configuration
```

---

## **Quick Reference: All Prompts in Order**

1. **Prompt 1:** Create Flask App with Products
2. **Prompt 2:** Create All CI/CD Files
3. **Prompt 3:** Test Flask App Locally (Shows commands)
4. **Prompt 3a:** ⚡ **Execute** - Run Local Testing Commands
5. **Prompt 4:** Create Dockerfile
6. **Prompt 5:** Create .dockerignore
7. **Prompt 6:** Build and Test Docker (Shows commands)
8. **Prompt 6a:** ⚡ **Execute** - Run Docker Build and Test Commands
9. **Prompt 7:** 🚀 **MCP** - Create GitHub Repository and Push Files
10. **Prompt 8:** 🚀 **MCP** - Verify GitHub Setup
11. **Prompt 9:** Create Azure Resources (Shows commands)
12. **Prompt 9a:** ⚡ **Execute** - Run Azure Resource Creation Commands
13. **Prompt 10:** Deploy and Configure Azure App (Shows steps)
14. **Prompt 10a:** ⚡ **Execute** - Run Azure Deployment Commands
15. **Prompt 11:** Create Azure Service Principal (Shows commands)
16. **Prompt 11a:** ⚡ **Execute** - Run Service Principal Creation Commands
17. **Prompt 12:** Add GitHub Secret (Manual - GitHub UI)
18. **Prompt 13:** Set Up Azure Deployment Center (Manual - Azure Portal)
19. **Prompt 14:** 🚀 **MCP** - Get Azure-Created Workflow
20. **Prompt 15:** Update Workflow with Service Principal
21. **Prompt 16:** 🚀 **MCP** - Update and Push Workflow
22. **Prompt 17:** 🚀 **MCP** - Verify Continuous Deployment
23. **Prompt 18:** 🚀 **MCP** - Test Automatic Deployment
24. **Prompt 19:** Verify Deployment Success

**🚀 = Uses MCP GitHub tools for automation**  
**⚡ = Execution prompt - runs the commands**

**Troubleshooting (use as needed):**
20. Docker Build Issues
21. Azure Login Issues
22. App Shows Default Azure Page
23. GitHub Actions Deployment Fails
24. Port Errors

---

## **Expected Flask App Structure**

After following all prompts, your Flask app should have:

**Routes:**
- `GET /` - Returns all products with categories
- `GET /health` - Health check endpoint
- `GET /products` - Returns all products
- `GET /products/<category>` - Returns products filtered by category (tv, mobile, smart-devices)

**Product Categories:**
- **TV:** Samsung 55" Smart TV, LG 65" OLED, Sony 43" 4K
- **Mobile:** iPhone 15 Pro, Samsung Galaxy S24, OnePlus 12
- **Smart Devices:** Amazon Echo Dot, Google Nest Hub, Apple HomePod

**Each Product Should Have:**
- id (unique identifier)
- name (product name)
- category (tv, mobile, smart-devices)
- price (in rupees)
- description (product description)

---

## **Tips for Using These Prompts**

1. **Copy and paste each prompt** into Cursor one at a time
2. **Wait for completion** before moving to the next prompt
3. **Verify each step** before proceeding
4. **Use troubleshooting prompts** (20-24) if you encounter issues
5. **Test locally first** before deploying to Azure
6. **Keep your Azure credentials secure** - never commit them to Git
7. **MCP GitHub tools** automate Git operations - no need for manual git commands
8. **MCP makes it faster** - create, update, and push files directly from Cursor

---

## **Summary**

These prompts will guide you through:
✅ Creating a Flask app with products (TV, Mobile, Smart Devices)
✅ Setting up Docker containerization
✅ **Automating GitHub operations using MCP** (create repo, push files, update files)
✅ Deploying to Azure App Service
✅ Setting up CI/CD with GitHub Actions
✅ Testing automatic deployment

**Total Prompts:** 19 main prompts + 4 execution prompts + 5 troubleshooting prompts = 28 prompts

**Prompt Types:**
- **Regular prompts:** Create files, show commands, explain steps
- **Execution prompts (⚡):** Actually run the commands
- **MCP prompts (🚀):** Use MCP tools for automation

**MCP Automation Benefits:**
- ✅ No manual git commands needed
- ✅ Create and push files directly from Cursor
- ✅ Update files and commit automatically
- ✅ Check workflow status programmatically
- ✅ Faster and more efficient workflow

Follow them sequentially for a complete deployment pipeline!
