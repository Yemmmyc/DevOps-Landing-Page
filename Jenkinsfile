pipeline {
  agent any

  environment {
    EC2_USER = 'ubuntu'
    EC2_HOST = '44.203.90.191'
    PEM_PATH = 'C:/JenkinsKeys/yem_jenkins.pem' // Adjust this path!
  }

  stages {
    stage('Clone Repository') {
      steps {
        git branch: 'main', url: 'https://github.com/Yemmmyc/DevOps-Landing-Page.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        bat 'docker build -t landing-page .'
      }
    }

    stage('Deploy to EC2') {
      steps {
        bat """
        echo Stopping old container on EC2...
        plink -batch -i "%SSH_KEY_PPK%" %SSH_USER%@%EC2_IP% "docker rm -f landing || true"

        echo Running new container...
        plink -batch -i "%SSH_KEY_PPK%" %SSH_USER%@%EC2_IP% "docker run -d -p 80:80 --name landing %DOCKER_IMAGE%"
        """
      }
    }
  }
}
