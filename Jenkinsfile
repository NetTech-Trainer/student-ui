pipeline {
    agent any
    tools {
        maven 'm3'
    }

    stages {
        stage('fetch') {
            steps {
                git credentialsId: 'myid', url: 'https://github.com/NetTech-Trainer/student-ui.git'
            }
        }
        stage('build') {
            steps {
               sh 'mvn clean package'
            }
        }
        stage('deploy') {
            steps {
               sh 'sudo cp /var/lib/jenkins/workspace/studentapp/target/studentapp-2.2-SNAPSHOT.war /opt/tomcat/webapps'
            }
        }
    }
}
