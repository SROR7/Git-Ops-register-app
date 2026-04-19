pipeline {
    agent { label 'jenkins-agent' }
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'Image tag from CI pipeline')
    }
    environment {
        GITOPS_REPO    = 'https://github.com/SROR7/Git-Ops-register-app'
        GIT_USER_NAME  = 'SROR7'
        GIT_USER_EMAIL = 'jenkins@example.com'
    }
    stages {
        stage('Validate') {
            steps {
                script {
                    if (!params.IMAGE_TAG?.trim()) {
                        error "IMAGE_TAG was not passed from CI pipeline"
                    }
                    echo "Deploying: sror/register-app-pipeline:${params.IMAGE_TAG}"
                }
            }
        }
        stage('CleanUp Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout GitOps Repo') {
            steps {
                git branch: 'main',
                    credentialsId: 'GitHub',
                    url: "${GITOPS_REPO}"
            }
        }
        stage('Update Deployment Tag') {
            steps {
                sh '''
                    cat deployment.yaml
                    sed -i "s/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g" deployment.yaml
                    cat deployment.yaml
                '''
            }
        }
        stage('Commit and Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'GitHub',
                    usernameVariable: 'GIT_USERNAME',
                    passwordVariable: 'GIT_PASSWORD'
                )]) {
                    sh '''
                        git config --global user.name "SROR7"
                        git config --global user.email "m7mdsror0@gmail.com"
                        git add deployment.yaml
                        git commit -m "Updated Deployment Manifest"
                    '''
                }
            }
        }
    }
}
