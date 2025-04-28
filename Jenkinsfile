pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/ankamraghu27/Hiring-app-argocd.git'
        GIT_BRANCH = 'main'
        GIT_USER = 'ankamraghu27'
        GIT_EMAIL = 'your-email@example.com'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    // Your Docker build steps go here, e.g.:
                    sh 'docker build -t raghuankam27/hiring-app:latest .'
                }
            }
        }

        stage('Checkout K8S manifest SCM') {
            steps {
                withCredentials([string(credentialsId: 'git', variable: 'GIT_TOKEN')]) {
                    script {
                        echo 'Checking out K8S manifest repository...'
                        sh """
                            git config --global user.name '${GIT_USER}'
                            git config --global user.email '${GIT_EMAIL}'
                            git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/${GIT_USER}/Hiring-app-argocd.git
                            git fetch --tags --force --progress -- https://github.com/${GIT_USER}/Hiring-app-argocd.git +refs/heads/*:refs/remotes/origin/*
                            git checkout ${GIT_BRANCH}
                        """
                    }
                }
            }
        }

        stage('Update K8S manifest & push to Repo') {
    steps {
        withCredentials([string(credentialsId: 'git', variable: 'GIT_TOKEN')]) {
            script {
                echo "Updating deployment.yaml with new image name and tag: ankamraghu27/hiring-app:${BUILD_NUMBER}"
                sh """
                    cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                    sed -i "s#image: .*/hiring-app:[0-9]*#image: ankamraghu27/hiring-app:${BUILD_NUMBER}#" /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                    cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                    git add .
                    git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                    git push https://${GIT_USER}:${GIT_TOKEN}@github.com/${GIT_USER}/Hiring-app-argocd.git ${GIT_BRANCH}
                """
            }
        }
    }
}

    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
