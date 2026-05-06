pipeline {
    agent any
    parameters {
        string(name: 'DOCKER_IMAGE_VERSION', defaultValue: '', description: 'Docker Image Version')
        choice(name: 'SERVICE', choices: ['backend', 'frontend'], description: 'Service to update')
    }
    stages {
        stage('Update deploy.yaml') {
            steps {
                dir("${params.SERVICE}") {
                    sh 'git checkout main'
                    sh "sed -i 's|dawitlee/beatbuddy-${params.SERVICE}:.*|dawitlee/beatbuddy-${params.SERVICE}:${params.DOCKER_IMAGE_VERSION}|g' deploy.yaml"
                    sh 'cat deploy.yaml'
                }
            }
        }
        stage('Commit & Push') {
            steps {
                sh 'git config user.name "jenkins"'
                sh 'git config user.email "jenkins@beatbuddy.com"'
                sh 'git add .'
                sh "git commit -m 'Update ${params.SERVICE} image to ${params.DOCKER_IMAGE_VERSION}'"
                withCredentials([usernamePassword(credentialsId: 'github-ssh-key', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                    sh '''
                        git remote set-url origin https://${GIT_USER}:${GIT_PASS}@github.com/Dawit-lee/beatbuddy-k8s.git
                        git push origin main
                    '''
                }
            }
        }
    }
}