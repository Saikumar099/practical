pipeline {
    agent none
      tools{
       git 'Git'
       maven 'maven3.8.6'
       //sonarqubescanner 'SonarQubeScanner'
      }
       stages{
           stage('checkout code') {
               agent any
               steps{
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Saikumar099/practical.git']]])  
               stash 'source'
               }
           }
            stage('maven build') {
               agent any
               steps{
                     sh 'mvn package'
                }
           }
           stage('sonarqube report') {
               agent {
                    label 'Docker Server'
               }
               environment{
                   scannerHome = tool 'SonarQubeScanner'
               }
               steps{
                    unstash 'source'
                    withSonarQubeEnv('sonarqube-9.1') { 
                       sh "${tool("SonarQubeScanner")}/bin/sonar-scanner -Dsonar.host.url=http://54.193.191.66:9000/ 
                        -Dsonar.projectKey=project-demo 
                        -Dsonar.projectName=project-demo 
                        -Dsonar.java.binaries=target/classes
                       //sh 'mvn clean install sonar:sonar -Dsonar.host.url=http://54.176.139.74:9000 -Dsonar.projectKey=project-demo -Dsonar.projectName=project-demo'
                }
             }
           }
           stage('upload artifacts to nexus') {
               agent {
                    label 'Docker Server'
               }
              steps{
                  nexusArtifactUploader artifacts: [[artifactId: 'java-web-app', 
                                           classifier: '', 
                                           file: 'target/java-web-app-1.0.war', 
                                           type: 'war']], 
                                           credentialsId: 'nexus', 
                                           groupId: 'com.mt', 
                                           nexusUrl: '54.193.191.66:8081/', 
                                           nexusVersion: 'nexus3', 
                                           protocol: 'http', 
                                           repository: 'practical-1', 
                                           version: '1.0'
               } 
           }
           stage('creating tomcat image with webapp') {
              agent {
                    label 'Docker Server'
              }
               steps{
                    unstash 'source'
                    sh 'docker build -t saikumar099/java-web-app:$BUILD_NUMBER .'
              }
           }
       }
}
