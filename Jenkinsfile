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
                                docker build -t ahmedzedan/bakehouseitismart:v${BUILD_NUMBER} .
                                docker push ahmedzedan/bakehouseitismart:v${BUILD_NUMBER}
                            '''
                        }
                }
            }
        }
    }
}
