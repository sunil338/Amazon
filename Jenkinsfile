pipeline {
    agent any
    tools {
        jdk 'JDK17'
        maven 'Maven3'
    }
    environment {
        SONARQUBE = 'sonarqube'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/YOUR_USER/YOUR_REPO.git'
            }
        }
        stage('Build & Unit Test') {
            steps {
                dir('Amazon') {  
                    sh 'mvn clean verify'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                dir('Amazon') {  
                    withSonarQubeEnv('sonarqube') {
                        sh 'mvn sonar:sonar -Dsonar.projectKey=myapp'
                    }
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Package WAR') {
            steps {
                dir('Amazon') {  
                    sh 'mvn package'
                    archiveArtifacts artifacts: 'target/*.war', fingerprint: true
                }
            }
        }
        stage('Deploy to Tomcat using Ansible') {
            steps {
                sshagent(['ansible_ssh_key']) {
                    sh '''
                      ansible-playbook ansible/deploy.yml -i ansible/inventory/hosts
                    '''
                }
            }
        }
    }
}
