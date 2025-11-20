pipeline {
    agent any

    environment {
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
        DOTNET_SKIP_FIRST_TIME_EXPERIENCE = '1'
        BUILD_CONFIGURATION = 'Release'
    }

    options {
        timestamps()
        ansiColor('xterm')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Restore') {
            steps {
                sh 'dotnet restore HelloWorld.csproj'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build HelloWorld.csproj -c ${BUILD_CONFIGURATION} --no-restore'
            }
        }

        stage('Test') {
            steps {
                script {
                    def hasTests = sh(
                        returnStatus: true,
                        script: 'ls *Tests.csproj >/dev/null 2>&1'
                    ) == 0

                    if (hasTests) {
                        sh '''
                            set -e
                            for proj in *Tests.csproj; do
                                dotnet test "$proj" -c ${BUILD_CONFIGURATION} --no-build
                            done
                        '''
                    } else {
                        echo 'No *Tests.csproj projects detected. Skipping tests.'
                    }
                }
            }
        }

        stage('Publish') {
            steps {
                sh 'dotnet publish HelloWorld.csproj -c ${BUILD_CONFIGURATION} -o build/publish --no-build'
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'build/publish/**', fingerprint: true, allowEmptyArchive: true
        }
        failure {
            echo 'Pipeline failed. Check logs above.'
        }
        always {
            cleanWs()
        }
    }
}

