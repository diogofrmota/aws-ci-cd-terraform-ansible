pipeline {
    agent { label 'jenkins-agent' }

    stages {
        stage('Checkout Repo') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'diogofrmota-github-credentials', url: 'https://github.com/diogofrmota/aws-infra-ansible-terraform-jenkins-ci-cd']])
            }
        }
        stage('Build Image') {
            steps {
                sh '''
                    cd website
                    docker build -t diogofrmota/diogofrmota-simple-website:latest .
                '''
            }
        }
        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'diogofrmota-dockerhub-credentials', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh '''
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        docker push diogofrmota/diogofrmota-simple-website:latest
                        docker logout
                    '''
                }
            }
        }
        stage('Deploy') {
            steps {
                    sh '''
                        docker rm -f diogofrmota-simple-website || true
                        docker run -d -p 80:80 --name diogofrmota-simple-website diogofrmota/diogofrmota-simple-website:latest
                    '''
        }
    }
}
}