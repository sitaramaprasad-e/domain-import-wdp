pipeline {

    // Instructs Jenkins to use the buildconductor docker image to run this pipeline
    
    agent {
        docker { 
            image 'greghodgkinson/jenkins-buildconductor-wdp:edge' 
            label 'docker'
            args '-u root'
        }
    }

    stages {
    
        // Pull down source code from Git repo
        
       stage('Checkout'){
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'load']], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/sitaramaprasad-e/domain-import-wdp.git']]])
            }
       }     
       
       // Export domain configuration
       
        stage('Build Domain') {
        
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'dp-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {            

                    sh "/buildconductor/wdp/run-automation.sh buildDomain DTEJDEV 10.11.13.69 5550 $USERNAME $PASSWORD" 
                }
            }
        }  
         stage("Stage with input") {
    steps {
        def userInput = false
        script {
            userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
            echo 'userInput: ' + userInput

            if(userInput == true) {
                // do action
            } else {
                // not do action
                echo "Action was aborted."
            }

        }    
    }  
}
        
        // Import domain configuration using deployment policy
        
        stage('Deploy Domain') {
            
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'dp-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {            

                    sh "/buildconductor/wdp/run-automation.sh deployDomain DTEJQA 10.11.13.69 5550 $USERNAME $PASSWORD sampleDeploymentPolicy" 
                }
            }
        }
    }
}
