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

        stage('SAST - Semgrep') {
             steps {
                echo 'Executando analise estatica de codigo (SAST) com Semgrep...'
                sh '''
                    docker run --rm -v "$(pwd):/src" returntocorp/semgrep \
                        semgrep scan --config auto --json --output semgrep-report.json --error
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'semgrep-report.json', allowEmptyArchive: true
                }
            }
        }

        stage('DAST - OWASP ZAP') {
            steps {
                echo 'Executando teste dinamico de seguranca (DAST) com OWASP ZAP...'
                sh '''
                    docker run --rm -v "$(pwd):/zap/wrk:rw" zaproxy/zap-stable \
                        zap-baseline.py -t http://182.0.0.20:3000 -r zap-report.html -I
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'zap-report.html', allowEmptyArchive: true
                }
            }
        }
    

        
        stage('Deploy') {
            steps {
                echo 'Enviando aplicação para a VM prod...'
                sshagent(['app']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no -r ./* vagrant@182.0.0.20:/home/vagrant/
                    '''
                }
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
