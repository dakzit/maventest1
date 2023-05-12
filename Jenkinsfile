pipeline {
    agent any
    tools{
        maven 'M2_HOME'
    }
    environment {
    registry = '782434001163.dkr.ecr.us-east-1.amazonaws.com/devops-terra'
    registryCredential = 'aws-credential'
    dockerimage = ''
  }
    stages {
        stage('Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/dakzit/maventest1.git'
            }
        }
        stage('SonarQube scan') {
            steps{
            withSonarQubeEnv('sonar') {
        sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=dakzit_geolocation1'        
            }   
            }
        }
        stage('Code Build') {
            steps {
                sh 'mvn clean install package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build Image') {
            steps {
                script{
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                } 
            }
        }
        stage('Deploy image') {
            steps{
                script{ 
                    docker.withRegistry("https://"+registry,"ecr:us-east-1:"+registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }  
    }
}