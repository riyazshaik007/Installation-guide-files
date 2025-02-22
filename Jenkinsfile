pipeline {

    agent any
        tools {
              maven 'M2_HOME'
            }
    stages {
         stage('checkout code') {
             steps {
                 git 'https://github.com/abdulkhadarjb/jenkins-task'
                
            }
        }
        stage('validate code') {
            steps {
                sh 'mvn validate'
            }
        }    
        stage('compile code') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Run unit test') {
            steps {
                sh 'mvn test'
            }
        }    
        stage('create build') {
            steps {
                sh 'mvn package'
            }
        }    
        stage('verify code') {
            steps {
                sh 'mvn verify'
            }
        }  
              stage("Maven Build"){
            steps{
                sh "mvn clean sonar:sonar package"
                
            }
        }
        stage('Upload War To Nexus'){
            steps{
                  nexusArtifactUploader artifacts: [
                       [
                            artifactId: 'myweb', 
                            classifier: '', 
                            file: "target/myweb-8.2.0.war", 
                            type: 'war'
                       ]
                  ], 
                  credentialsId: 'nexus3', 
                  groupId: 'in.javahome', 
                  nexusUrl: '172.31.40.8:8081', 
                  nexusVersion: 'nexus3', 
                  protocol: 'http', 
                  repository: 'sample-releases', 
                  version: '8.2.0'  
              }
            }
        stage("deploy-dev"){
            steps{
                sshagent(['tomcat-new']) {
                sh """
                    scp -o StrictHostKeyChecking=no target/myweb-8.2.0.war  ec2-user@172.31.41.172:/home/ec2-user/apache-tomcat-9.0.64/webapps/
                    
                    ssh ec2-user@172.31.41.172 /home/ec2-user/apache-tomcat-9.0.64/bin/shutdown.sh
                    
                    ssh ec2-user@172.31.41.172 /home/ec2-user/apache-tomcat-9.0.64/bin/startup.sh
                
                """
            }
            
            }
        }

    }
    post {
        always {
            echo 'Always'
        }
        success {
            echo 'Only on SUCCESS'
        }
          failure {
                   emailext body: 'jenkins pipeline failure', subject: 'jenkins pipeline failure', to: 'skabdul.marolix@gmail.com'
                   echo "Email sent for jenkins build fail"
        }
    }  
} 
