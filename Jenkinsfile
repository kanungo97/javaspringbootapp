pipeline {
    agent {
        node {
            label 'jenkins-slave-node'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.6/bin:$PATH"
    }
    stages {
        stage("Build") {
            steps {
                echo "----------- Build started ----------"
                sh 'mvn clean package -Dmaven.test.skip=true'
                echo "------------ Build completed ----------"
            }
        }
        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'Sonar-scanner-meportal'
            }
            steps {
                withSonarQubeEnv('Sonarqube-server-meportal') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}
