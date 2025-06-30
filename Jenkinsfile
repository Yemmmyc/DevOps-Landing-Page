pipeline {
    agent any

    environment {
        IMAGE_NAME = 'landing-page'
        EC2_USER = 'ubuntu'
        EC2_HOST = '44.203.90.191'
        PPK_PATH = 'C:\\JenkinsKeys\\yem_jenkins.ppk'
        PLINK = 'C:\\JenkinsKeys\\plink.exe'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Yemmmyc/DevOps-Landing-Page.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME% ."
            }
        }

        stage('Deploy to EC2') {
            steps {
                bat """
                    echo Removing old container on EC2...
                    %PLINK% -i "%PPK_PATH%" %EC2_USER%@%EC2_HOST% "docker rm -f landing || true"

                    echo Running new container...
                    %PLINK% -i "%PPK_PATH%" %EC2_USER%@%EC2_HOST% "docker run -d -p 80:80 --name landing %IMAGE_NAME%"
                """
            }
        }
    }
}
