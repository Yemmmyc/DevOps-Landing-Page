pipeline {
    agent any

    environment {
        PLINK = 'C:\\JenkinsKeys\\plink.exe'
        PPK = 'C:\\JenkinsKeys\\yem_jenkins.ppk'
        EC2_USER = 'ubuntu'
        EC2_HOST = '44.203.90.191'
        HOSTKEY = 'ssh-ed25519 255 SHA256:A8IKU1jnJQ1O/3V3dp/R+JU02k6IyQ787NbgkpRADtk'
    }

    stages {
        stage('Deploy to EC2') {
            steps {
                script {
                    bat """
                    echo Removing old container on EC2...
                    "%PLINK%" -batch -i "%PPK%" -hostkey "%HOSTKEY%" %EC2_USER%@%EC2_HOST% "docker rm -f landing || true"

                    echo Running new container on EC2...
                    "%PLINK%" -batch -i "%PPK%" -hostkey "%HOSTKEY%" %EC2_USER%@%EC2_HOST% "docker run -d -p 80:80 --name landing landing-page"
                    """
                }
            }
        }
    }
}

