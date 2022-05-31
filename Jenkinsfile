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

        stage('Create Tomcat Docker Image'){
            steps {
                sh "pwd"
                sh "ls -a"
                sh "docker build webapp -t tomcatsamplewebapp:${env.BUILD_ID}"
            }
        }

        stage('Create MySQL Docker Image'){
            steps {
                sh "pwd"
                sh "ls -a"
                sh "docker build mysql -t mysqlsample:${env.BUILD_ID}"
            }
        }

        stage('Create nginx Docker Image'){
            steps {
                sh "pwd"
                sh "ls -a"
                sh "docker build nginx -t nginxsample:${env.BUILD_ID}"
            }
        }
    }
}