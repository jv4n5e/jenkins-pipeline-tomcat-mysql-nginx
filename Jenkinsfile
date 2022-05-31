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

        stage('Create Tomcat Docker Image'){
            steps {
                sh "pwd"
                sh "ls -a"
                sh "docker build webapp -t tomcatsamplewebapp:${env.BUILD_ID}"
            }
        }

        stage('Deploy Tomcat Docker Image (port 8090)'){
            steps {
                sh "docker container run --publish 8090:8080 --detach --name tomcat-8090 tomcatsamplewebapp:${env.BUILD_ID}"
            }
        }

        stage('Create MySQL Docker Image'){
            steps {
                sh "docker build mysql -t mysqlsample:${env.BUILD_ID}"
            }
        }

        stage('Deploy MySQL Docker Image (port 3306)'){
            steps {
                sh "docker container run --publish 3306:3306 --detach --name mysql-3306 mysqlsample:${env.BUILD_ID}"
            }
        }

        stage('Create nginx Docker Image'){
            steps {
                sh "docker build nginx -t nginxsample:${env.BUILD_ID}"
            }
        }

        stage('Deploy nginx Docker Image (port 80)'){
            steps {
                sh "docker container run --publish 80:80 --detach --name nginx-80 nginxsample:${env.BUILD_ID}"
            }
        }

        stage('Approval to kill'){
            steps {
                input "Can we kill the running containers?"
                sh "docker container rm nginx-80 mysql-3306 tomcat-8090 -f"
            }
        }
    }
}