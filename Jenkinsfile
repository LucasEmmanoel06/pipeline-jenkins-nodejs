pipeline {
    agent any

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Instalando dependências do projeto...'
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                echo 'Executando build do projeto...'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                echo 'Executando testes...'
                sh 'npm test'
            }
        }
    }

    post {
        success {
            echo '✓ Pipeline executado com sucesso!'
        }

        failure {
            echo '✗ Pipeline falhou!'
        }

        always {
            echo 'Pipeline finalizado.'
        }
    }
}
