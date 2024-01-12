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
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "------------ Build completed ----------"
            }
        }

          stage("Test Stage"){
            steps{
                echo "----------- unit test started ----------"
                sh 'mvn surefire-report:report'
                echo "----------- unit test Completed -----------"
            }
        }
       /* stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'Sonar-scanner-meportal'
            }
            steps {
                withSonarQubeEnv('Sonarqube-server-meportal') {
                    sh "${scannerHome}/bin/sonar-scanner -X"
                }
            }
        }
        stage("Quality Gate"){
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') { 
                        def qg = waitForQualityGate() 
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
        */
                stage("Artifact Publish") {
            steps {
                script {
                    echo '------------- Artifact Publish Started ------------'
                    def server = Artifactory.newServer url:"https://meportal97.jfrog.io//artifactory" ,  credentialsId:"jfrog-cred"
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "staging/(*)",
                                "target": "release-local-artifacts/{1}",
                                "flat": "false",
                                "props" : "${properties}",
                                "exclusions": [ "*.sha1", "*.md5"]
                            }
                        ]
                    }"""
                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)
                    echo '------------ Artifact Publish Ended -----------'  
                }
            }   
        }

                stage(" Create Docker Image ") {
            steps {
                script {
                    echo '-------------- Docker Build Started -------------'
                    app = docker.build("meportal97.jfrog.io/meportal-docker-local/myapp:1.0")
                    echo '-------------- Docker Build Ended -------------'
                }
            }
        }

        stage (" Docker Publish "){
            steps {
                script {
                        echo '---------- Docker Publish Started --------'  
                        docker.withRegistry("https://meportal97.jfrog.io/", 'jfrog-cred'){
                        app.push()
                        echo '------------ Docker Publish Ended ---------'  
                    }    
                }
            }
        }



    }
}




    

