pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/dotnet/sdk:8.0' // Updated to .NET 8 SDK
            args '-u root -v /tmp:/tmp' 
            reuseNode true
        }
    }

    environment {
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
        DOTNET_NOLOGO = 'true'
        DOTNET_SKIP_FIRST_TIME_EXPERIENCE = '1'
        NUGET_PACKAGES = '/tmp/nuget/packages'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Prepare') {
            steps {
                sh '''
                    mkdir -p /tmp/nuget/packages
                    chmod -R 777 /tmp/nuget
                '''
            }
        }

        stage('Restore') {
            steps {
                sh 'dotnet restore --packages /tmp/nuget/packages'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build --configuration Release --no-restore'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test --no-restore --verbosity normal' // Fixed typo in --no-restore
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}