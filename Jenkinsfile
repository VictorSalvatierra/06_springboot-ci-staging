pipeline {
    agent any

    environment {
        STAGING_USER = 'root'
        STAGING_HOST = 'ssh_server2'
        STAGING_SERVER = "${STAGING_USER}@${STAGING_HOST}"
        ARTIFACT_NAME = 'demo-0.0.1-SNAPSHOT.jar'
        ARTIFACT_PATH = "target/${ARTIFACT_NAME}"
        REMOTE_PATH = '/var/local/staging'
        HEALTH_ENDPOINT = "http://${STAGING_HOST}:8181/health"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/nicolasth21/springboot-app'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
                sh 'ls -la target/'
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
                echo "📦 Enviando artefacto a ${STAGING_SERVER}"
                sh 'ls -la target/' 
                sh 'scp target/${ARTIFACT_NAME} $STAGING_SERVER:/var/local/staging/'
                sh 'ssh $STAGING_SERVER "nohup java -jar /var/local/staging/${ARTIFACT_NAME} > /dev/null 2>&1 &"'
                //sh 'ls -la target/' 
                //sh "scp target/${ARTIFACT_NAME} $STAGING_SERVER:/var/local/staging/"
                //sh "ssh $STAGING_SERVER \"nohup java -jar /var/local/staging/${ARTIFACT_NAME} > /dev/null 2>&1 &\""
                // Lanzar la app y guardar logs
               // sh """
                //    ssh $STAGING_SERVER '
                //        nohup java -jar /var/local/staging/${ARTIFACT_NAME} > /var/local/staging/app.log 2>&1 &
                //    '
                //"""

                // Verificar si el proceso Java está corriendo
                sh "ssh $STAGING_SERVER 'ps aux | grep ${ARTIFACT_NAME}'"
                sh "ssh ${STAGING_SERVER} 'jar tf /var/local/staging/${ARTIFACT_NAME} | grep DemoApplication'"
               // sh "ssh ${STAGING_SERVER} 'tail -n 50 /var/local/staging/app.log'"
        
                // Mostrar últimos logs de arranque
                sh "ssh $STAGING_SERVER 'tail -n 20 /var/local/staging/app.log'"
        
                // Verificar si el puerto 8181 está escuchando
                sh "ssh $STAGING_SERVER 'netstat -tuln | grep 8181 '"


            }
        }

         stage('Validate Deployment') {
            steps {
                script {
                    def retries = 20
                    def delay = 10
                    def success = false
        
                    for (int i = 0; i < retries; i++) {
                        def result = sh(script: "ssh $STAGING_SERVER 'curl --fail --silent http://localhost:8181/health'", returnStatus: true)
                        if (result == 0) {
                            echo "✅ Servicio disponible en localhost:8181/health desde ssh_server2"
                            success = true
                            break
                        } else {
                            echo "⏳ Esperando servicio... intento ${i + 1}/${retries}"
                            sleep time: delay, unit: 'SECONDS'
                        }
                    }
        
                    if (!success) {
                        error "❌ El servicio no respondió en localhost:8181/health desde ssh_server2"
                    }
                }
            }
        }
    }
}

