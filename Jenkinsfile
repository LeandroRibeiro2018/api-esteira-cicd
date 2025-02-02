pipeline {
    agent any

    environment {
        MAVEN_HOME = '/usr/share/maven'              // Caminho padrão do Maven no Linux
        PATH = "${env.PATH}:${MAVEN_HOME}/bin"         // Adiciona o Maven ao PATH
        MAVEN_OPTS = "-Dmaven.test.failure.ignore=true"// Ignora falhas nos testes
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
        stage('Test') {
            steps {
                echo 'Executando testes...'
                sh 'mvn test -e -X'
            }
            post {
                always {
                    archiveArtifacts artifacts: '**/target/surefire-reports/*.xml', allowEmptyArchive: true
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deploy') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Matando instâncias anteriores na porta 8090 (se houver)...'
                // Finaliza qualquer processo que esteja usando a porta 8090
                sh 'fuser -k 8090/tcp || true'
                
                echo 'Iniciando o aplicativo Spring Boot em background...'
                // Inicia a aplicação para escutar em todas as interfaces (0.0.0.0) na porta 8090
                sh 'nohup java -jar target/*.jar --server.address=0.0.0.0 --server.port=8090 > output.log 2>&1 &'
                
                // Aguarda alguns segundos para a aplicação iniciar
                sh 'sleep 10'
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