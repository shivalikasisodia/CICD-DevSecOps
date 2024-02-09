@Library('jenkins-shared-library') _
pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    parameters{

        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        string(name: 'ImageName', description: "name of the docker build", defaultValue: 'javapp')
        string(name: 'ImageTag', description: "tag of the docker build", defaultValue: 'v1')
        string(name: 'DockerHubUser', description: "name of the Application", defaultValue: 'shivisis')
        string(name: 'Region', description: "Region of ECR", defaultValue: 'us-east-1')

    }
    environment{

        ACCESS_KEY = credentials('AWS_ACCESS_KEY_ID')
        SECRET_KEY = credentials('AWS_SECRET_KEY_ID')
        AWS_CRED = credentials('aws-auth')
    }
     stages{

         stage('Git Checkout'){
        when { expression {  params.action == 'create' } }            
            steps{
            gitCheckout(
                branch: "main",
                url: "https://github.com/shivalikasisodia/CICD-DevSecOps.git"
            )
            }
        }
         stage('Integration Test maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{ 
                   mvnIntegrationTest()
               }
            }
        }
        //  stage('Static Code Analysis: Sonarqube'){
        //  when { expression {  params.action == 'create' } }
        //     steps{
        //        script{ 
        //            def SonarQubeCredentialsId = 'sonar-cred'
        //            staticCodeAnalysis(SonarQubeCredentialsId)
        //        }
        //     }
        // }
        //  stage('Quality Gate Status: Sonarqube'){
        //  when { expression {  params.action == 'create' } }
        //     steps{
        //        script{ 
        //            def SonarQubeCredentialsId = 'sonarqube-api'
        //            QualityGateStatus(SonarQubeCredentialsId)
        //        }
        //     }
        // }
         stage('Maven build: maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{ 
                  mavenBuild()
               }
            }
        }
        stage('Docker Image Build'){
         when { expression {  params.action == 'create' } }
            steps{
               script{ 
                  dockerBuild("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }
        stage('Docker Image Scan: trivy '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImageScan("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }
         stage('Docker Image Push : DockerHub '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImagePush("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }   
         stage('Docker Image Cleanup : DockerHub '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImageCleanup("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        } 
        stage('AWS Configure'){
         when { expression {  params.action == 'create' } }
            steps{   
                withCredentials([[
                  $class: 'AmazonWebServicesCredentialsBinding',
                  credentialsId: 'aws-auth',
                  accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                  secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                    sh "aws ec2 describe-instances --region ${params.Region}"
                  }
            }
       }   
       

         
     }
}