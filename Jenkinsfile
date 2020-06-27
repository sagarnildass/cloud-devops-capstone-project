pipeline {
     agent any
     stages {
         stage('Build') {
              steps {
                  sh 'echo Building...'
              }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e ./app/src/*.html'
              }
         }
         stage('Build Docker Image') {
              steps {
                  sh 'docker build -t capstone-app-sagarnil .'
              }
         }
         stage('Push Docker Image') {
              steps {
                  withDockerRegistry([url: "", credentialsId: "dockerhub"]) {
                      sh "docker tag capstone-app-sagarnil sagarnildass/capstone-app-sagarnil"
                      sh 'docker push sagarnildass/capstone-app-sagarnil'
                  }
              }
         }
         stage('Deploying') {
              steps{
                  echo 'Deploying to AWS...'
                  withAWS(credentials: 'aws-static', region: 'ap-south-1') {
                      sh "aws eks --region ap-south-1 update-kubeconfig --name capstoneclustersagarnil"
                      sh "kubectl config use-context arn:aws:eks:ap-south-1:960920920983:cluster/capstoneclustersagarnil"
                      sh "kubectl apply -f capstone-k8s.yaml"
                      sh "kubectl get nodes"
                      sh "kubectl get deployments"
                      sh "kubectl get pod -o wide"
                      sh "kubectl get service/capstone-app-sagarnil"
                  }
              }
        }
        stage('Checking if app is up') {
              steps{
                  echo 'Checking if app is up...'
                  withAWS(credentials: 'aws-static', region: 'ap-south-1') {
                     sh "curl ad0e6a88870a9477989eb79393197b59-2120449898.ap-south-1.elb.amazonaws.com:9080"
                  }
               }
        }
        stage("Cleaning up") {
              steps{
                    echo 'Cleaning up...'
                    sh "docker system prune"
              }
        }
     }
}
