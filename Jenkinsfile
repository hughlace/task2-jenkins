pipeline {
    agent any
    environment {
        MYSQL_ROOT_PASSWORD = credentials("MYSQL_ROOT_PASSWORD")
    }
    stages {
        stage("Build") {
            steps {
                sh '''
                docker build -t agray998/task2-db db
                docker build -t agray998/task2-app flask-app
                '''
            }
        }
        stage("Push") {
            steps {
                sh '''
                docker push agray998/task2-db
                docker push agray998/task2-app
                '''
            }
        }
        stage("Deploy") {
            steps {
                sh '''
                scp nginx/nginx.conf jenkins@adam-deploy:/home/jenkins/nginx.conf
                ssh jenkins@adam-deploy <<EOF
                export MYSQL_ROOT_PASSWORD="${MYSQL_ROOT_PASSWORD}"
                docker pull agray998/task2-db
                docker pull agray998/task2-app
                docker pull agray998/task2-nginx
                docker stop nginx && echo "stopped nginx" || echo "nginx not running"
                docker stop flask-app && echo "stopped flask-app" || echo "flask-app not running"
                docker stop mysql && echo "stopped mysql" || echo "mysql not running"
                docker rm nginx && echo "stopped nginx" || echo "nginx not running"
                docker rm flask-app && echo "stopped flask-app" || echo "flask-app not running"
                docker rm mysql && echo "stopped mysql" || echo "mysql not running"
                docker network rm task2-net && echo "task2-net removed" || echo "task2-net not present"
                docker network create task2-net
                docker volume inspect task2-vol && echo "vol exists" || docker volume create task2-vol
                docker run -d --name mysql --network task2-net -e MYSQL_ROOT_PASSWORD="${MYSQL_ROOT_PASSWORD}" -v task2-vol:/var/lib/mysql agray998/task2-db
                docker run -d --name flask-app --network task2-net -e MYSQL_ROOT_PASSWORD="${MYSQL_ROOT_PASSWORD}" agray998/task2-app
                docker run -d --name nginx --network task2-net -p 80:80 --mount type=bind,source=/home/jenkins/nginx.conf,target=/etc/nginx/nginx.conf nginx:alpine
                '''
            }
        }
    }
}