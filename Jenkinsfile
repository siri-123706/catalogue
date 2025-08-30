pipeline {
     // agent any 
    agent {
        label 'AGENT-1'
    }
    environment {
        appVersion = ''
        REGION = "us-east-1"
        ACC_ID = "203981192510"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
        //COURSE = 'jenkins'
    }

    // }
    options { // pipeline expries 30 mint
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds() // not parallel to pipelines at a time so, one complted after another complted .
        // prevents multiple runs of this job at the same time (avoids conflicts like 2 builds pushing same Docker tag).
    }
     parameters {
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
        
    }
   // build
   stages {
        stage('Read package.json') {
            steps {
                script {
                   
                    def packageJSON = readJSON file: 'package.json'
                     appVersion = packageJSON.version
                    echo "Package Version: ${appVersion }"

                }
            }
        }
        stage('install dependencies') {
            steps {
                script {
                    sh """
                        npm install
                    """

                }
            }
        }
        stage('unit testing') {
            steps {
                script {
                    sh """
                        echo "unit tests"
                    """

                }
            }
        }
        stage('Sonar scan') { 
            environment {
                scannerHome = tool 'sonar-7.2'
            }          
            steps {
                script {
                    // sonar server environment
                    withSonarQubeEnv(installationName:'sonar-7.2') { // ID configured in Configure System
                         sh "${scannerHome}/bin/sonar-scanner"

                    }
                }
            }

        } 
        // enable webhook in sonarqube server and wait for results
        // stage ("Quality Gate") {
        //     steps {
        //         timeout(time: 1 unit: 'HOURS') {
        //         waitForQuality abortPipeline: true }
        //     }
       
        // }                     
        stage('docker build') {
            steps { 
                script {
                    withAWS(credentials: 'aws-cli', region: 'us-east-1') {
                        sh """
                        aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                        docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                        docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                        """
                    }
                }

            }
        
        }
            
        stage('trigger deploy') {
            when {
                expression { params.delpoy }
            }
            steps {
                script {
                    build job: 'catalogue-cd', 
                    parameters: [
                       string(name: 'appVersion', value: "${appVersion}"),
                       string(name: 'deploy_to', value: 'dev')
                              
                    ],
                    propagate: false,    //if deploy fails, this build won’t fail.
                    wait: false   // don’t wait for deploy pipeline, continue immediately.  
                }
            }
       }  
    }       
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir() // delete post build pipeline in workspace  
        }
        success { 
            echo 'hello success'
        }
        failure { 
            echo 'hello failure'
        }
    }
}

