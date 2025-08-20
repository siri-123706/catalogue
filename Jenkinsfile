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
        disableConcurrentBuilds() // not parallel to pipelines at a time so, one complted after another complted
    }
    /*  parameters {
        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')
        booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')
        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    } */
   // build
   stages {
        stage('Read package.json') {
            steps {
                script {
                    // Read the entire package.json file into a Groovy Map
                    def packageJSON = readJSON file: 'package.json'

                    // Access a specific property, for example, the 'version'
                     appVersion = packageJSON.version

                    // Print the extracted version
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
        
        stage('docker build') {
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                        sh """
                        aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ACC_ID}
                        .dkr.ecr.us-east-1.amazonaws.com
                        docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                        docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                        """
                     }
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

