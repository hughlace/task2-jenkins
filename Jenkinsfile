pipeline {
    agent any
    environment {
        MYSQL_ROOT_PASSWORD = credentials("MYSQL_ROOT_PASSWORD")
    }
    stages {
        stage("Build") {
            steps {
                sh '''
                docker build -t hughston05/task2-db db
                docker build -t hughston05/task2-app flask-app
                '''
            }
        }
        stage("Push") {
            steps {
                sh '''
                docker push hughston05/task2-db
                docker push hughston05/task2-app
                '''
            }
        }
       stage('Deploy') {
            steps {
                sh '''
                kubectl apply -f .
                sleep 60
                kubectl get services
                '''
            }
        }
    }
}