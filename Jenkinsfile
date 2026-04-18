pipeline {
  agent { label 'jenkins-agent' }

  environment {
      APP_NAME = "register-app-pipeline"
      IMAGE_TAG = "latest"
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
                cat deployment.yaml
                sed -i "s/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g" deployment.yaml
                cat deployment.yaml
              """
          }
      }

      stage("Push changes") {
          steps {
              withCredentials([string(credentialsId: 'JENKINS_API_TOKEN', variable: 'TOKEN')]) {
                  sh '''
                      curl -v -k \
                      -X POST \
                      -H "cache-control: no-cache" \
                      -H "content-type: application/x-www-form-urlencoded" \
                      --data "IMAGE_TAG=1.0.0-51" \
                      "http://ec2-51-20-65-24.eu-north-1.compute.amazonaws.com:8080/job/gitops-register-app-cd/buildWithParameters?token=gitops-token"
                  '''
              }
          }
      }
  }
}
