
## 🚀 CI/CD Pipeline Workflow

### 🎯 **Goal**
Automate the build, testing, and deployment of the application to ensure the highest quality and minimize human intervention.

### ⚙️ **Pipeline Stages**
1. **Code Push and Version Control**
   - GitHub as the version control system
   - Trigger CI/CD pipeline upon push/merge to the `main` branch

2. **Build Stage**
   - Build the application using **Maven**
   - Resolve and manage dependencies
   - Generate an executable JAR file

3. **Test Stage**
   - Run unit tests using **JUnit** to ensure code integrity
   - Check for security vulnerabilities using built-in security scanners

4. **Artifact Packaging**
   - Package the application and prepare it for deployment
   - Save build artifacts to facilitate rollback if necessary

5. **Deployment Stage**
   - Deploy to **AWS EC2** using automated deployment scripts
   - Verify deployment and run smoke tests to validate the application

---

## 🛠️ Technologies

- **Java** – Backend logic
- **Spring Boot** – Application framework
- **Amazon Web Services (AWS) EC2** – Cloud hosting and deployment
- **Thymeleaf & Thymeleaf Fragments** – HTML templating engine
- **HTML5, CSS, JavaScript** – Frontend UI/UX
- **Spring MVC** – Model-View-Controller architecture
- **JDBC** – Database connectivity
- **H2 Database Engine** – In-memory database
- **JUnit** – Unit and integration testing
- **Spring Security** – Authentication and authorization
- **Twitter Bootstrap** – UI styling
- **Maven** – Dependency management
- **GitHub Actions** – CI/CD pipeline automation
- **Docker** – Containerization (optional for future enhancements)

---

## 🔥 CI/CD Workflow with GitHub Actions

### 📝 **GitHub Actions Configuration**

A `.github/workflows/ci-cd-pipeline.yml` file is used to define the CI/CD workflow.

```yaml
name: CI/CD Pipeline for BoardgameListingWebApp

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v2

    - name: Set up JDK 11
      uses: actions/setup-java@v2
      with:
        java-version: '11'
        distribution: 'adopt'

    - name: Build with Maven
      run: mvn clean package

    - name: Run Tests
      run: mvn test

    - name: Upload Build Artifact
      uses: actions/upload-artifact@v2
      with:
        name: boardgame-app
        path: target/*.jar

  deploy:
    needs: build
    runs-on: ubuntu-latest

    steps:
    - name: Download Build Artifact
      uses: actions/download-artifact@v2
      with:
        name: boardgame-app

    - name: Deploy to AWS EC2
      env:
        PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
        HOST: ${{ secrets.EC2_HOST }}
        USER: ${{ secrets.EC2_USER }}
      run: |
        echo "$PRIVATE_KEY" > private_key.pem
        chmod 600 private_key.pem
        scp -o StrictHostKeyChecking=no -i private_key.pem *.jar $USER@$HOST:/home/ec2-user/
        ssh -o StrictHostKeyChecking=no -i private_key.pem $USER@$HOST << 'EOF'
          sudo systemctl stop boardgame-app
          sudo mv /home/ec2-user/*.jar /opt/boardgame-app.jar
          sudo systemctl start boardgame-app
        EOF
```

---

## 📦 Features

### ✅ **Application Functionality**
- Full-Stack Application with Board Game Management
- UI components built with Thymeleaf and styled with Bootstrap
- CRUD Operations with JDBC
- Authorization using Spring Security with role-based permissions:
  - **Non-members:** Can only view board game lists and reviews
  - **Users:** Can add board games and write reviews
  - **Managers:** Can edit and delete reviews
- Deployed on AWS EC2 with auto-scaling and failover for high availability

### 🔥 **CI/CD Pipeline Highlights**
- Automated build, test, and deployment with GitHub Actions
- Code pushed to `main` triggers the CI/CD pipeline
- Automatic rollback if deployment fails
- Secure SSH access to EC2 for deployment

---

## 🎮 How to Run Locally

1. **Clone the Repository**
```bash
git clone https://github.com/your-username/BoardgameListingWebApp.git
cd BoardgameListingWebApp
```

2. **Set Up Database**
- Schema and initial data are configured in `schema.sql`
- H2 Database is used for local development and testing

3. **Run the Application**
```bash
mvn spring-boot:run
```

4. **Access the Application**
```
http://localhost:8080
```

---

## 🔐 Default Credentials
For initial access, use these credentials:
- **User:** `bugs` | **Password:** `bunny` (user role)
- **Manager:** `daffy` | **Password:** `duck` (manager role)

You can also sign up and create your own account to explore different roles!

---

## 🧪 Running Tests

To run unit tests and verify functionality:
```bash
mvn test
```

---

## 🚀 Deploying to AWS EC2 Manually

If needed, manually deploy the application to your AWS EC2 instance:
```bash
scp -i /path/to/your/key.pem target/boardgame-app.jar ec2-user@your-ec2-ip:/home/ec2-user/
```
Then restart the service:
```bash
ssh -i /path/to/your/key.pem ec2-user@your-ec2-ip
sudo systemctl restart boardgame-app
```

---

