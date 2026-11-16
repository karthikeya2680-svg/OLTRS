node {
    def gitRepo = 'https://github.com/karthikeya2680-svg/OLTRS.git'
    def dockerUser = 'karthikeya004'
    def imageName = "${dockerUser}/oltrs-app"
    def imageTag = "${env.BUILD_NUMBER}"
    def dockerCredsId = 'dockerhub-creds'

    try {
        stage('Checkout') {
            git branch: 'master', url: gitRepo
        }

        stage('Build & Test') {
            bat 'mvn clean test package'
        }

        stage('Docker Build') {
            bat "docker build -t ${imageName}:${imageTag} -t ${imageName}:latest ."
        }

        stage('Docker Push') {
            withCredentials([usernamePassword(
                credentialsId: dockerCredsId,
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )]) {
                bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"
                bat "docker push ${imageName}:${imageTag}"
                bat "docker push ${imageName}:latest"
                bat "docker logout"
            }
        }

        currentBuild.result = 'SUCCESS'
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    }
}
