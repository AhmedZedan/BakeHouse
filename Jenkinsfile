pipeline {
    agent { label 'jenkins-slave' }
    stages {
        stage('build') {
            steps {
                echo 'build'
                script{
                        if (BRANCH_NAME == "dev" || BRANCH_NAME == "test" || BRANCH_NAME == "prod") {
                                withCredentials([usernamePassword(credentialsId: 'dockerhub-pass', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                                    sh '''
                                        docker login -u ${USERNAME} -p ${PASSWORD}
                                        docker build -t ahmedzedan/bakehouse-iti-smart:v${BUILD_NUMBER} .
                                        docker push ahmedzedan/bakehouse-iti-smart:v${BUILD_NUMBER}
                                        echo ${BUILD_NUMBER} > ../build.txt
                                    '''
                                }
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                echo 'deploy'
                script {
                         if (BRANCH_NAME == "release") {
                                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                                    sh '''
                                        export BUILD_NUMBER=$(cat ../build.txt)
                                        mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                                        cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                                        rm -f Deployment/deploy.yaml.tmp
                                        kubectl apply -f Deployment --kubeconfig ${KUBECONFIG}
                                    '''
                                }
                         }
                    }
                }
          }
    }
}
