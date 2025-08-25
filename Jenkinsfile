pipeline {
    agent any
    environment {
        STAGING_SERVER = 'root@ssh_server2'
        ARTIFACT_NAME = 'demo-0.0.1-SNAPSHOT.jar'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/nicolasth21/springboot-app'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Code Quality') {
            steps {
                sh 'mvn checkstyle:check'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Code Coverage') {
            steps {
                sh 'mvn jacoco:report'
            }
        }
        stage('Deploy to Staging') {
            steps {
                sh 'scp target/${ARTIFACT_NAME} $STAGING_SERVER:/var/local/staging/'
                sh 'ssh $STAGING_SERVER "nohup java -jar /var/local/staging/${ARTIFACT_NAME} > /dev/null 2>&1 &"'
            }
        }
        stage('Validate Deployment') {
            steps {
                sh 'sleep 20'
                sh 'curl --fail http://STAGING_SERVER:8181/health'
            }
        }
    }
}
