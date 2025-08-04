pipeline {
    agent any

    environment {
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
        DOTNET_SKIP_FIRST_TIME_EXPERIENCE = 'true'
        DOTNET_NOLOGO = 'true'
        NUGET_PACKAGES = '${WORKSPACE}/.nuget/packages'
        SOLUTION_FILE = 'SeleniumIde.sln'  // Update with your solution file
        CONFIGURATION = 'Release'
        TEST_RESULTS_DIR = 'TestResults'
    }

    stages {
        stage('Restore') {
            steps {
                script {
                    echo 'Restoring NuGet packages...'
                    bat """
                        dotnet restore "%SOLUTION_FILE%" --packages "${NUGET_PACKAGES}"
                    """
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building project...'
                    // Using MSBuild plugin
                    msbuild(
                        solutionFile: SOLUTION_FILE,
                        commandLineArgs: "/p:Configuration=${CONFIGURATION} /p:Platform=\"Any CPU\" /p:RestorePackages=false /p:DeployOnBuild=true /p:DeployDefaultTarget=WebPublish",
                        targets: 'Rebuild',
                        toolsVersion: 'Current'
                    )
                    
                    // Alternatively using dotnet CLI
                    // bat "dotnet build \"%SOLUTION_FILE%\" --configuration %CONFIGURATION% --no-restore"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo 'Running tests...'
                    bat """
                        if not exist "%TEST_RESULTS_DIR%" mkdir "%TEST_RESULTS_DIR%"
                        dotnet test "%SOLUTION_FILE%" \
                            --configuration "%CONFIGURATION%" \
                            --no-build \
                            --logger "trx;LogFileName=%TEST_RESULTS_DIR%\\TestResults.trx" \
                            --logger "console;verbosity=normal" \
                            --results-directory "%TEST_RESULTS_DIR%" \
                            --filter "Category=Integration"  // Filter for integration tests
                    """
                }
            }
        }

        stage('Publish Test Results') {
            steps {
                script {
                    echo 'Publishing test results...'
                    // Convert TRX to JUnit format for better Jenkins reporting
                    bat """
                        dotnet tool install --global trx2junit --version 1.6.0 || echo "trx2junit already installed"
                        trx2junit "%TEST_RESULTS_DIR%\\*.trx"
                    """
                    
                    // Archive test results
                    junit allowEmptyResults: true, testResults: "%TEST_RESULTS_DIR%\\*.xml"
                    archiveArtifacts artifacts: "%TEST_RESULTS_DIR%\\*.*", allowEmptyArchive: true
                }
            }
        }
    }

    post {
        always {
            script {
                echo 'Cleaning up workspace...'
                cleanWs()
            }
        }
        success {
            script {
                echo 'Build and tests completed successfully!'
            }
        }
        failure {
            script {
                echo 'Build or tests failed!'
            }
        }
    }
}