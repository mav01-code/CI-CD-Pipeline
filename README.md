# CI/CD Pipeline with Jenkins, AWS, and Git

This project sets up a Continuous Integration and Continuous Deployment (CI/CD) pipeline using **Jenkins**, **AWS**, and **Git** to automate code building, testing, and deployment.

## 🛠️ Technologies Used
- **Jenkins**: Automation server for building, testing, and deploying applications.
- **AWS**: Cloud platform used for deployment (EC2, S3, CodeDeploy).
- **Git**: Version control system (GitHub/GitLab/Bitbucket).
- **Docker (Optional)**: Containerized deployments.
- **AWS CodeDeploy**: Deployment service for automated deployments.

---

## 🚀 Pipeline Workflow

1. **Code Commit**: Developer pushes code to Git repository.
2. **Jenkins Trigger**: Jenkins detects changes and starts the build process.
3. **Build & Test**: Jenkins compiles the code and runs unit tests.
4. **Artifact Storage**: Build artifacts are stored (S3/Nexus).
5. **Deployment to AWS**: AWS CodeDeploy deploys the application to EC2 or ECS.
6. **Monitoring & Logs**: Deployment status and logs are monitored in Jenkins and AWS CloudWatch.

---

## 🔧 Setup Instructions

### 1️⃣ Prerequisites
- An **AWS account** with IAM roles for EC2, S3, and CodeDeploy.
- **Jenkins installed** on an EC2 instance or local server.
- **GitHub/GitLab repository** with your application code.

### 2️⃣ Install and Configure Jenkins
1. Install Jenkins on an AWS EC2 instance:
   ```sh
   sudo apt update && sudo apt install openjdk-11-jdk -y
   wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
   sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
   sudo apt update && sudo apt install jenkins -y
   sudo systemctl start jenkins && sudo systemctl enable jenkins
   ```
2. Install required Jenkins plugins:
   - Git Plugin
   - Pipeline Plugin
   - AWS CodeDeploy Plugin

3. Configure **Jenkins Credentials**:
   - Add AWS credentials (IAM access key & secret).
   - Add GitHub credentials (SSH key or OAuth token).

4. Set up a **Jenkins Pipeline Job**:
   - Create a new **Pipeline** job.
   - Add a `Jenkinsfile` to your Git repository.

---

## 📜 Jenkinsfile (Pipeline Script)
```groovy
pipeline {
    agent any
    environment {
        AWS_REGION = 'us-east-1'
        S3_BUCKET = 'my-deployment-bucket'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/user/repo.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'  // Modify based on your project
            }
        }
        stage('Upload to S3') {
            steps {
                sh 'aws s3 cp target/app.jar s3://$S3_BUCKET/'
            }
        }
        stage('Deploy') {
            steps {
                sh 'aws deploy create-deployment --application-name MyApp --deployment-group-name MyGroup --s3-location bucket=$S3_BUCKET,key=app.jar,bundleType=zip'
            }
        }
    }
}
```

---

## 🔥 Deployment to AWS
1. **Configure AWS CodeDeploy**:
   - Create an **S3 bucket** for artifacts.
   - Set up an **IAM Role** with permissions.
   - Define an **AppSpec.yml** file for deployment.

2. **Deploy the application**:
   ```sh
   aws deploy create-deployment \
       --application-name MyApp \
       --deployment-group-name MyGroup \
       --s3-location bucket=my-deployment-bucket,key=app.jar,bundleType=zip
   ```

---

## 🏆 Benefits
  - Automates build, test, and deployment  
  - Reduces manual errors and deployment time  
  - Ensures consistency across environments  
