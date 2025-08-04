pipeline {
    agent any

    environment {
        DOTNET_CLI_TELEMETRY_OPTOUT = '1' // Optional: Disables .NET CLI telemetry
    }

    stages {
        stage('Restore') {
            steps {
                script {
                    echo 'Restoring NuGet packages...'
                    bat 'dotnet restore' // For Windows agents
                    // If using Linux agent, use: sh 'dotnet restore'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building the project...'
                    bat 'dotnet build --configuration Release --no-restore'
                    // For Linux: sh 'dotnet build --configuration Release --no-restore'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo 'Running tests...'
                    bat 'dotnet test --no-restore --verbosity normal'
                    // For Linux: sh 'dotnet test --no-restore --verbosity normal'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed - cleaning up'
            // Optional: Add cleanup steps here
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}