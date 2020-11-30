pipeline {
   agent any
    stages {
      stage('Git Checkout') {
         steps {
            git 'https://github.com/Nikithathni/ForexPay.git'
        }
    }
       stage('Build Approval')
        {
        steps{
         slackSend baseUrl: 'https://hooks.slack.com/services/', channel: 'slackaproval', color: 'bad', message: "${env.BUILD_URL}", tokenCredentialId: 'slackaproval', username: 'admin'
         script{
           def userInput
           try {
             userInput = input(id: 'userInput', message: 'Build Approval', 
             submitterParameter: 'submitter',
             submitter:'buildapprover', 
             parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: 'Build Approval', name: 'env']])
             } catch(err) {
             def user = err.getCauses()[0].getUser()
             userInput = false
             echo "Aborted by: [${user}]"
            }
         }
         //echo ("Env: "+userInput['env'])
         //echo ("submitted by: "+userInput['submitter'])
        }
        }
    stage('Build') {
        steps {
            withSonarQubeEnv('sonar') {
                sh '/opt/apache-maven-3.6.3/bin/mvn clean verify sonar:sonar -Dmaven.test.skip=true'
            }
        }
    }  
       stage('Build Deploy')
        {
        steps{
         slackSend baseUrl: 'https://hooks.slack.com/services/', channel: 'slackaproval', color: 'bad', message: "${env.BUILD_URL}", tokenCredentialId: 'slackaproval', username: 'admin'
         script{
           def userInput
           try {
             userInput = input(id: 'userInput', message: 'Build Deploy', 
             submitterParameter: 'submitter',
             submitter:'builddeployer', 
             parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: 'Build Deploy', name: 'env']])
             } catch(err) {
             def user = err.getCauses()[0].getUser()
             userInput = false
             echo "Aborted by: [${user}]"
            }
         }
         //echo ("Env: "+userInput['env'])
         //echo ("submitted by: "+userInput['submitter'])
        }
        }
       stage ('Deploy') {
        steps {
            sh '/opt/apache-maven-3.6.3/bin/mvn clean deploy -Dmaven.test.skip=true'
        }
    }
    stage ('Release') {
        steps {
            sh 'export JENKINS_NODE_COOKIE=dontkillme ;nohup java -jar $WORKSPACE/target/*.jar &'
        }
    }
}
}
