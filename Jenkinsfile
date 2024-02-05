@Library('jenkins-shared-library') _
pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    parameters{

        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        // string(name: 'ImageName', description: "name of the docker build", defaultValue: 'javapp')
        // string(name: 'ImageTag', description: "tag of the docker build", defaultValue: 'v1')
        // string(name: 'DockerHubUser', description: "name of the Application", defaultValue: 'shivisis')
    }
     stages{

        stage("Cleanup Workspace"){
            steps{
                cleanWs()
            }

        }
         stage('Git Checkout'){
        when { expression {  params.action == 'create' } }            
            steps{
            gitCheckout(
                branch: "main",
                url: "https://github.com/shivalikasisodia/CICD-DevSecOps.git"
            )
            }
        }
         stage('Unit Test maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   mvnTest()
               }
            }
        }
         stage('Build project'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   mvnBuildPackage()
               }
            }
        }
         
     }
}