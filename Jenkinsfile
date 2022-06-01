pipeline {
    agent any
    stages {
        stage('Build Application') {
            steps {
                sh 'mvn -f webapp/pom.xml clean package'
            }
            post {
                success {
                    echo "Now Archiving the Artifacts...."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

       stage('Cleaning Docker (removing all unused containers)'){
            steps {
                sh '''
                    docker container rm $(docker container ls -a | grep -v "Up " | awk '(NR>1)' | awk '{print $1}') || true
                '''
            }
        }

        stage('Create custom nginx Docker Image'){
            steps {
                sh "docker image build nginx -t nginxcustom:1.0"
            }
        }

        stage('Deploy custom nginx Docker Image (port 80) with its own VPN and a bind point.'){
            steps {
                sh "docker network create nginxnet"
                sh "mkdir -p nginxbind"
                sh "cd nginxbind"
                sh "pwd"
                sh '''
                    docker run --publish 80:80 --detach --name nginx-80 --mount type=bind,source=$(pwd),target=/usr/share/nginx/html --network nginxnet nginxcustom:1.0
                '''
            }
        }
        
        stage('Create Tomcat Docker Image'){
            steps {
                sh "docker build webapp -t tomcatsamplewebapp:${env.BUILD_ID};"
            }
        }

        stage('Deploy Tomcat Docker Image (port 8090) with its own VPN'){
            steps {
                sh "docker network create tomcatnet"
                sh "docker run --publish 8090:8080 --detach --name tomcat-8090 --network tomcatnet tomcatsamplewebapp:${env.BUILD_ID};"
            }
        }

        stage('Create MySQL Docker Image'){
            steps {
                sh "docker build mysql -t mysqlsample:${env.BUILD_ID};"
            }
        }

        stage('Deploy MySQL Docker Image (port 3306) with its own VPN'){
            steps {
                sh "docker network create mysqlnet"
                sh "docker run --publish 3306:3306 --detach --name mysql-3306 --network mysqlnet --mount source=mysqlvolume,destination=/var/lib/mysql mysqlsample:${env.BUILD_ID};"
            }
        }

        stage('Create custom Python Docker Image'){
            steps {
                sh "docker build python -t pythoncustom:1.0;"
            }
        }

        stage('Deploy custom Python Docker Image with its own VPN'){
            steps {
                sh "docker network create pythonnet"
                sh "docker run --name pythoncustom --network pythonnet pythoncustom:1.0;"
            }
        }

        stage('Approval to kill'){
            steps {
                input "Can we kill the running containers and their networks?"
                sh "docker container rm nginx-80 mysql-3306 tomcat-8090 pythoncustom -f; docker network rm nginxnet mysqlnet tomcatnet pythonnet || true"
            }
        }
    }
    post {
        failure {
            sh "docker container rm nginx-80 mysql-3306 tomcat-8090 pythoncustom -f; docker network rm nginxnet mysqlnet tomcatnet pythonnet || true"
        }
    }
}