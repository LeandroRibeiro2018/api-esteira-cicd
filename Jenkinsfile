pipeline {
    agent any

    environment {
        MAVEN_HOME = '/opt/maven/apache-maven-3.9.6'  // Defina o caminho correto do Maven no Linux
        PATH = "${env.PATH}:${MAVEN_HOME}/bin"  // Adiciona o Maven ao PATH
        MAVEN_OPTS = "-Dmaven.test.failure.ignore=true"  // Ignora falhas de teste para continuar a execução
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
                sh '"${MAVEN_HOME}/bin/mvn" clean install -e -X'  // Usando sh para Linux
            }
        }

        stage('Test') {
            steps {
                echo 'Executando testes...'
                sh '"${MAVEN_HOME}/bin/mvn" test -e -X'  // Executa os testes
            }
            post {
                always {
                    archiveArtifacts artifacts: '**/target/surefire-reports/*.xml', allowEmptyArchive: true  
                    junit '**/target/surefire-reports/*.xml'  // Analisa os relatórios de teste
                }
            }
        }

        stage('Run Application') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Iniciando o aplicativo Spring Boot...'
                sh '"${MAVEN_HOME}/bin/mvn" spring-boot:run'  // Executa a aplicação no Linux
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
