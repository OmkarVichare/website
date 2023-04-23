pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-east-1:cred'
       appRegistry = "513486244896.dkr.ecr.us-east-1.amazonaws.com/capstone"
       capstoneRegistry = "https://513486244896.dkr.ecr.us-east-1.amazonaws.com"
       cluster = "CapstoneCluster"
        service = "CapstoneService"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/OmkarVichare/website.git'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'cred', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
