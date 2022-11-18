pipeline {
    agent any
      //def mavenHome =  tool name: "Maven-3.8.6", type: "maven"
      //def mavenCMD = "${mavenHome}/bin/mvn"
    tools{
        maven 'maven3.8.6'
    }
       stages{
           stage('checkout code'){
               steps{
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Saikumar099/practical.git']]])  
               }
           }
            stage('maven build'){
                steps{
                     sh 'mvn package'
                }
           }
           stage('sonarqube report'){
               environment{
                   scannerHome = tool 'SonarQubeScanner'
               }
                steps{
                    withSonarQubeEnv('sonarqube-9.1') { 
                       sh "${tool("SonarQubeScanner")}/bin/sonar-scanner -Dsonar.host.url=http://13.56.233.39:9000 -Dsonar.projectKey=project-demo -Dsonar.projectName=project-demo"
                       sh 'mvn clean install sonar:sonar -Dsonar.host.url=http://13.56.233.39:9000 -Dsonar.projectKey=project-demo -Dsonar.projectName=project-demo'
                }
             }
           }
           stage('upload artifacts to nexus'){
               steps{
                   sh 'mvn deploy'
               } 
           }
           stage('creating tomcat image with webapp'){
               steps{
                   sh 'docker build -t '
               }
           }
       }
}
