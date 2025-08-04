pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/dotnet/sdk:7.0' // или по-нова версия
            args '-v /tmp:/tmp' // опционално: монтиране на временна директория
            reuseNode true // използва същия работен нод за всички стейджове
        }
    }

    environment {
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
        DOTNET_NOLOGO = 'true'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // извлича кода от source control
            }
        }

        stage('Restore') {
            steps {
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build --configuration Release --no-restore'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test --no-restore --verbosity normal'
                // За код покритие:
                // sh 'dotnet test --collect:"XPlat Code Coverage"'
            }
        }
    }

    post {
        always {
            junit '**/TestResults/*.xml' // ако тестовете генерират JUnit репорти
            // archiveArtifacts artifacts: '**/bin/Release/**/*.dll', fingerprint: true
            cleanWs() // почиства работното пространство
        }
    }
}