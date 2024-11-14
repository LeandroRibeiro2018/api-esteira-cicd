pipeline {
    agent any

    environment {
        MAVEN_HOME = 'C:\\Develop\\apache-maven-3.9.6'  // Defina o caminho do Maven aqui, sem o /bin
        PATH = "${env.PATH};${MAVEN_HOME}\\bin"  // Adiciona o Maven ao PATH
        MAVEN_OPTS = "-Dmaven.test.failure.ignore=true"  // Ignora falhas de teste para o pipeline continuar e gerar relatórios
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
                bat '"%MAVEN_HOME%\\bin\\mvn" clean install -e -X'  // Inclui opções de erro detalhado e depuração
            }
        }

        stage('Test') {
            steps {
                echo 'Executando testes...'
                bat '"%MAVEN_HOME%\\bin\\mvn" test -e -X'  // Executa testes com detalhes
            }
            post {
                always {
                    archiveArtifacts artifacts: '**/target/surefire-reports/*.xml', allowEmptyArchive: true  // Armazena relatórios de teste
                    junit '**/target/surefire-reports/*.xml'  // Analisa relatórios de teste
                }
            }
        }

        stage('Run Application') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Iniciando o aplicativo Spring Boot...'
                bat '"%MAVEN_HOME%\\bin\\mvn" spring-boot:run'
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
