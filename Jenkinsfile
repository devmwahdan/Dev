pipeline {
    agent any

    environment {
        DOTNET_SDK_IMAGE = 'mcr.microsoft.com/dotnet/sdk:8.0'
        CONTAINER_WORKDIR = '/workspace'
    }
    stages {
        stage('Build') {
            steps {
                sh """
                    set -e
                    docker run --rm -v "\${PWD}":${CONTAINER_WORKDIR} -w ${CONTAINER_WORKDIR} \\
                        ${DOTNET_SDK_IMAGE} /bin/bash -c \\
                        "dotnet restore && dotnet build --no-restore"
                """
            }
        }

        stage('Deliver') {
            steps {
                sh """
                    set -e
                    docker run --rm -v "\${PWD}":${CONTAINER_WORKDIR} -w ${CONTAINER_WORKDIR} \\
                        ${DOTNET_SDK_IMAGE} /bin/bash -c \\
                        "dotnet publish HelloWorld --no-restore -o published"
                """
            }
            post {
                success {
                    archiveArtifacts 'published/*.*'
                }
            }
        }
    }
}