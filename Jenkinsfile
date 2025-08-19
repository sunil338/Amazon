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



        stage('Package WAR') {
            steps {
                dir('Amazon') {   // 👈 again inside Amazon
                    sh 'mvn package'
                    archiveArtifacts artifacts: 'Amazon-Web/target/*.war', fingerprint: true
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('Amazon') {
                    sh 'ansible-playbook -i ansible/inventory.ini ansible/ansible_playbook.yml'
                }
            }
        }
    }
}
