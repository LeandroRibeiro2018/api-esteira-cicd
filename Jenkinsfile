pipeline {
    agent any

    environment {
        MAVEN_HOME = '/usr/share/maven'
        PATH = "${env.PATH}:${MAVEN_HOME}/bin"
        MAVEN_OPTS = "-Dmaven.test.failure.ignore=true"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Realizando checkout do código...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Compilando o aplicativo Spring Boot...'
                sh 'mvn clean install -e -X'
            }
        }

        stage('Kill Process on Port 8090') {
            steps {
                script {
                    echo 'Verificando e finalizando processos na porta 8090...'
                    sh '''
                    PID=$(lsof -t -i:8090)
                    if [ ! -z "$PID" ]; then
                        echo "Matando processo $PID..."
                        kill -9 $PID
                    else
                        echo "Nenhum processo rodando na porta 8090."
                    fi
                    '''
                }
            }
        }

        stage('Deploy') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Iniciando o aplicativo Spring Boot...'
                sh '''
                nohup java -jar target/*.jar --server.port=8090 > output.log 2>&1 &
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline concluído!'
        }
        success {
            echo 'Pipeline executado com sucesso.'
        }
        failure {
            echo 'Pipeline falhou.'
        }
    }
}
