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
                git branch: 'master', url: 'https://github.com/sunil338/Amazon.git'
            }
        }

        stage('Build & Unit Test') {
            steps {
                 dir('Amazon') {
                    sh 'mvn clean install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
               dir('Amazon') {   // 👈 same here
                    withSonarQubeEnv('sonarqube') {
                        sh 'mvn sonar:sonar -Dsonar.projectKey=myapp'
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Package WAR') {
            steps {
                dir('Amazon') {   // 👈 again inside Amazon
                    sh 'mvn package'
                    archiveArtifacts artifacts: 'target/*.war', fingerprint: true
                }
            }
        }

        stage('Deploy to Tomcat using Ansible') {
            steps {
                sh '''
                  ansible-playbook Amazon/ansible/ansible_playbook.yml -i Amazon/ansible/inventory.ini
                '''
            }
        }
    }
}
