pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "dehyamohammed/my-app"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_access_id') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        
        
       stage('DeployToProduction') {
    when {
        branch 'master'
    }
    steps {
        input 'Deploy to Production?'
        milestone(1)

        withKubeConfig([credentialsId: 'kubeconfig']) {
    sh 'kubectl config view --minify'
    sh 'kubectl cluster-info'
    sh 'kubectl apply -f my-app-deploy.yaml'
}
    }
}
    }
}
