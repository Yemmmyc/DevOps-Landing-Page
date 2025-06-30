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
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
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
