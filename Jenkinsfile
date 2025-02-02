pipeline {
    agent any

    environment {
        MAVEN_HOME = '/usr/share/maven' // Caminho padrão do Maven no Linux
        PATH = "${env.PATH}:${MAVEN_HOME}/bin" // Adiciona Maven ao PATH
        MAVEN_OPTS = "-Dmaven.test.failure.ignore=true" // Ignora falhas nos testes
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
                sh 'mvn clean install -e -X' // Usa o Maven diretamente do PATH
            }
        }

        stage('Test') {
            steps {
                echo 'Executando testes...'
                sh 'mvn test -e -X' // Executa testes
            }
            post {
                always {
                    archiveArtifacts artifacts: '**/target/surefire-reports/*.xml', allowEmptyArchive: true // Salva relatórios de teste
                    junit '**/target/surefire-reports/*.xml' // Analisa os testes
                }
            }
        }

        stage('Run Application') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Iniciando o aplicativo Spring Boot...'
                sh 'mvn spring-boot:run'
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
