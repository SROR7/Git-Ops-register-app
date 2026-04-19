pipeline {
    agent { label 'jenkins-agent' }

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'Image tag from CI pipeline')
    }

    environment {
        APP_NAME       = 'register-app-pipeline'
        DOCKER_USER    = 'sror'
        GITOPS_REPO    = 'https://github.com/SROR7/Git-Ops-register-app/'
        GIT_USER_NAME  = 'jenkins-bot'
        GIT_USER_EMAIL = 'jenkins@example.com'
    }

    stages {

        stage('Validate') {
            steps {
                script {
                    if (!params.IMAGE_TAG?.trim()) {
                        error "IMAGE_TAG was not passed from CI pipeline"
                    }
                    echo "Deploying: ${DOCKER_USER}/${APP_NAME}:${params.IMAGE_TAG}"
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
                    credentialsId: 'github',
                    url: "${GITOPS_REPO}"
            }
        }

        stage('Update Deployment Tag') {
            steps {
                sh '''
                    echo "Before:"
                    grep "image:" deployment.yaml

                    sed -i "s|image: .*register-app-pipeline:.*|image: sror/register-app-pipeline:${IMAGE_TAG}|g" deployment.yaml

                    echo "After:"
                    grep "image:" deployment.yaml
                '''
            }
        }

        stage('Commit and Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github',
                    usernameVariable: 'GIT_USERNAME',
                    passwordVariable: 'GIT_PASSWORD'
                )]) {
                    sh '''
                        git config user.name  "$GIT_USER_NAME"
                        git config user.email "$GIT_USER_EMAIL"

                        git add deployment.yaml

                        if git diff --cached --quiet; then
                            echo "No changes — tag already up to date"
                        else
                            git commit -m "ci: update image tag to ${IMAGE_TAG}"
                            git push https://${GIT_USERNAME}:${GIT_PASSWORD}@https://github.com/SROR7/Git-Ops-register-app.git HEAD:main
                        fi
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "GitOps repo updated with tag ${params.IMAGE_TAG} — ArgoCD will sync"
        }
        failure {
            echo "CD pipeline failed — deployment.yaml was NOT updated"
        }
    }
}
