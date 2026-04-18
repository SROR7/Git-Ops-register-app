pipeline {
  agent { label 'Jenkins-Agent' }

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
              withCredentials([usernamePassword(credentialsId: 'GitHub', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                  sh """
                    git config --global user.name "SROR7"
                    git config --global user.email "m7mdsror0@gmail.com"

                    git add deployment.yaml
                    git commit -m "Updated Deployment Manifest" || echo "No changes to commit"

                    git remote set-url origin https://$GIT_USERNAME:$GIT_PASSWORD@github.com/SROR7/Git-Ops-register-app
                    git push origin main
                  """
              }
          }
      }
  }
}
