pipeline {
    agent any 
        tools {
            maven 'maven3'
            jdk 'java8'
        }
        stages {
            stage ('init') {
                steps {
                    echo "Initialize pipeline"
                    sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    '''
                }
            }
            
            stage ('build') {
                steps {
                    echo "Build started"
                    sh '''
                        mvn clean package checkstyle:checksyle
                    '''
                }
                post {
                    success {
                        archiveArtifacts '**/*.war'
                        junit '**/target/surefire-reports/*.xml'
                        checkstyle canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
                    }
                }
            }
            
            stage ('deploy_to_stag') {
                steps {
                    build 'deploy-to-stag'
                }
            }
            
            stage ('Deploy to Production'){
                steps{
                    timeout(2){
                        input message:'Approve PRODUCTION Deployment?'
                    }

                build job: 'deploy-to-prod'
                }
                post {
                    success {
                        echo 'Code deployed to Production.'
                    }
                    failure {
                        echo ' Deployment failed.'
                    }
                }
            }
        }
}