pipeline {
  agent any

  environment {
    EC2_USER = 'ubuntu'
    EC2_HOST = '54.197.208.175'
    PEM_PATH = 'C:\\Users\\IT-WORKSTATION\\Documents\\cloud_native.pem' // Adjust this path!
  }

  stages {
    stage('Clone Repository') {
      steps {
        git 'https://github.com/Yemmmyc/DevOps-Landing-Page.git'
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
        echo Removing old container on EC2...
        ssh -o StrictHostKeyChecking=no -i "%PEM_PATH%" %EC2_USER%@%EC2_HOST% "docker rm -f landing || true"

        echo Running new container...
        ssh -i "%PEM_PATH%" %EC2_USER%@%EC2_HOST% "docker run -d -p 80:80 --name landing landing-page"
        """
      }
    }
  }
}
