pipeline {
    agent any

    environment {
        DOTNET_SDK_IMAGE = 'mcr.microsoft.com/dotnet/sdk:8.0'
        DOCKER_ARGS = '-u root:root'
    }
    stages {
        stage('Build') {
            steps {
                script {
                    docker.image(env.DOTNET_SDK_IMAGE).inside(env.DOCKER_ARGS) {
                        sh 'dotnet restore'
                        sh 'dotnet build --no-restore'
                    }
                }
            }
        }

        stage('Deliver') {
            steps {
                script {
                    docker.image(env.DOTNET_SDK_IMAGE).inside(env.DOCKER_ARGS) {
                        sh 'dotnet publish HelloWorld --no-restore -o published'
                    }
                }
            }
            post {
                success {
                    archiveArtifacts 'published/*.*'
                }
            }
        }
    }
}