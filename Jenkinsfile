pipeline {
     // agent any 
    agent {
        label 'AGENT-1'
    }
    // environment {
    //     COURSE = 'jenkins'

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
        stage('Build') {
            steps {
                script{
                   // echo 'Building..'
                   sh """
                   echo "hello world"
                   sleep 10
                   env
                   """
                }
                
            } 
        }
        stage('Test') { 
            steps {
                script{
                    echo 'Testing..'
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

