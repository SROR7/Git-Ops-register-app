pipeline {
    agent { label 'jenkins-agent' }

    environment {
        APP_NAME = "register-app-pipeline"
        IMAGE_TAG = "latest"
        CD_URL = "http://51.20.65.24:8080/job/gitops-register-app-cd/buildWithParameters"
    }

    stages {

        stage('CleanUp WorkSpace') {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main',
                    credentialsId: 'github',
                    url: 'https://github.com/Ashfaque-9x/gitops-register-app'
            }
        }

        stage("Update Deployment Tags") {
            steps {
                sh """
                    echo "Before update:"
                    cat deployment.yaml

                    sed -i "s|${APP_NAME}.*|${APP_NAME}:${IMAGE_TAG}|g" deployment.yaml

                    echo "After update:"
                    cat deployment.yaml
                """
            }
        }

        stage("Push changes to CD") {
            steps {
                sh """
                    curl -v -k \
                    -X POST \
                    -H "cache-control: no-cache" \
                    -H "content-type: application/x-www-form-urlencoded" \
                    --data "IMAGE_TAG=${IMAGE_TAG}" \
                    "${CD_URL}?token=gitops-token"
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}
