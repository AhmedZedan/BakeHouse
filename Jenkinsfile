pipeline {
    agent { label 'jenkins-slave' }
    stages {
        stage('build') {
            steps {
                echo 'build'
                script{
                        withCredentials([usernamePassword(credentialsId: 'dockerhub-pass', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                            sh '''
                                docker login -u ${USERNAME} -p ${PASSWORD}
                                docker build -t ahmedzedan/bakehouse-iti-smart:v${BUILD_NUMBER} .
                                docker push ahmedzedan/bakehouse-iti-smart:v${BUILD_NUMBER}
                            '''
                        }
                }
            }
        }
        stage('deploy') {
            steps {
                echo 'deploy'
                script {
                        withAWS(credentials: 'AWS-Credentials', region: 'us-east-1') {
                            sh '''
                                export BUILD_NUMBER=${BUILD_NUMBER}
                                mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                                cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                                rm -f Deployment/deploy.yaml.tmp
                                aws eks update-kubeconfig --name Terraform-EKS-AWS-cluster --region us-east-1
                                kubectl apply -f Deployment
                            '''
                        }
                    }
                }
          }
    }
}
