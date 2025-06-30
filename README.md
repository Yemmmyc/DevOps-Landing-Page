README.md – DevOps Landing Page Project
🚀 DevOps Landing Page – CI/CD with Docker, Jenkins, and AWS
This is a simple, fanciful HTML/CSS landing page deployed via a full DevOps pipeline using:

✅ Version Control: GitHub

✅ CI/CD: Jenkins (on Windows)

✅ Containerization: Docker

✅ Deployment Platform: AWS EC2

✅ Trigger: GitHub Webhook

📂 Project Structure

DevOps-Landing-Page/
├── index.html        # Fanciful landing page
├── styles.css        # Styling for dark theme
├── Dockerfile        # Container definition using nginx
├── Jenkinsfile       # Pipeline-as-code
└── README.md         # Project guide

🎨 Preview
This project renders a dark-themed, modern landing page with:

A bold heading

Short project description

A styled button for GitHub

🛠️ Tools Used
Tool	Purpose
GitHub	Code hosting & version control
Jenkins	CI/CD automation (on Windows)
Docker	Containerize and run the web page
EC2 (Ubuntu)	Host the Docker container
GitHub Webhooks	Trigger build on push

📦 Docker Setup
🐳 Dockerfile

dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html

💬 Build & Run Locally

docker build -t landing-page .
docker run -d -p 80:80 landing-page

⚙️ Jenkins Pipeline
🧪 Jenkinsfile
groovy

pipeline {
    agent any

    environment {
        IMAGE = 'yemisi76/landing-page:latest'
        PLINK = 'C:\\JenkinsKeys\\plink.exe'
        PPK = 'C:\\JenkinsKeys\\yem_jenkins.ppk'
        EC2_USER = 'ubuntu'
        EC2_HOST = '44.203.90.191'
        HOSTKEY = 'ssh-ed25519 255 SHA256:A8IKU1jnJQ1O/3V3dp/R+JU02k6IyQ787NbgkpRADtk'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/Yemmmyc/DevOps-Landing-Page.git',
                        credentialsId: '3ec38073-4bfc-4441-a706-72abb0f7cb66'
                    ]]
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE% ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    bat """
                    echo Logging into Docker Hub...
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    docker push %IMAGE%
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    bat """
                    echo Removing old container on EC2...
                    "%PLINK%" -batch -i "%PPK%" -hostkey "%HOSTKEY%" %EC2_USER%@%EC2_HOST% "sudo docker rm -f landing || true"

                    echo Running new container on EC2...
                    "%PLINK%" -batch -i "%PPK%" -hostkey "%HOSTKEY%" %EC2_USER%@%EC2_HOST% "sudo docker run -d -p 80:80 --name landing %IMAGE%"
                    """
                }
            }
        }
    }
}


🌍 Hosting on AWS EC2
🔑 EC2 Requirements
Ubuntu instance (t2.micro recommended)

Port 80 open in security group

Docker installed on EC2:

sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

🔐 Jenkins Credential
Add SSH credentials in Jenkins:

Field	Value
Kind	SSH Username with private key
ID	ec2-key
Username	ubuntu
Private Key	Paste contents of .pem file

✅ Deployment Process
You push code to GitHub

GitHub triggers webhook

Jenkins pulls code, builds Docker image

Jenkins SSHs into EC2 and deploys

EC2 serves the page at:
http://<EC2_PUBLIC_IP>

📚 Learnings & Highlights
Full CI/CD from code to deployment

Docker containerization with NGINX

Jenkins integration with GitHub and EC2

Automated deployment via SSH

Secure credential management

📎 Resources
Jenkins Official Docs

Docker Docs

AWS EC2 Docs

GitHub Webhooks

👩‍💻 Author
Yemisi – IT Technical Support & DevOps Enthusiast
📫 GitHub: Yemmmmyc
