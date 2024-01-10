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
        stage("build"){
            steps {
                echo "----------- build started ----------"
                sh 'mvn clean package -Dmaven.test.skip=true'
                echo "------------ build complted ----------"
            }
        }
    }
}
stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'Sonar-scanner-meportal'
            }
            steps{
                withSonarQubeEnv('SonarQube-Server-meportal') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }