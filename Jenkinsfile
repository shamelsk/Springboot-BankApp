@Library('Shared') _
pipeline {
    agent any
    
    environment{
        SONAR_HOME = tool "Sonar"
    }
    
    parameters {
        string(name: 'DOCKER_TAG', defaultValue: '', description: 'Setting docker image for latest push')
    }
    
    stages {
        
        stage("Workspace cleanup"){
            steps{
                script{
                    cleanWs()
                }
            }
        }
        
        stage('Git: Code Checkout') {
    steps {
        script {
            clone(
                "https://github.com/shamelsk/Springboot-BankApp.git",
                "DevOps"
            )
        }
    }
}
        
        stage("Trivy: Filesystem scan"){
            steps{
                script{
                    trivy_scan()
                }
            }
        }

        stage("OWASP: Dependency check"){
            steps{
                script{
                    owasp_dependency()
                }
            }
        }
        
        stage("SonarQube: Code Analysis") {
    steps {
        script {
            sonarqube_scan()
        }
    }
}
        
        stage("SonarQube: Code Quality Gates"){
            steps{
                script{
                    sonarqube_code_quality()
                }
            }
        }

        stage('Docker: Build Image') {
    steps {
        script {
            docker_build(
                imageName: "bankapp",
                imageTag: params.DOCKER_TAG
            )
        }
    }
}
        
        stage('Docker: Push to DockerHub') {
    steps {
        script {
            docker_push(
                sourceImage: "bankapp",
                imageName: "shamel1012/springboot-bankapp",
                imageTag: params.DOCKER_TAG,
                credentialsId: "dockerHubCred"
            )
        }
    }
}
    }
    post{
        success{
            archiveArtifacts artifacts: '*.xml', followSymlinks: false
            build job: "BankApp-CD", parameters: [
                string(name: 'DOCKER_TAG', value: "${params.DOCKER_TAG}")
            ]
        }
    }
}
