pipeline {
  agent any
 
  tools {
  maven 'Maven3'
  }
  stages {
    stage ('Build') {
      steps {
      sh 'mvn clean install -f MyWebApp/pom.xml'
      }
    }
    //stage ('Code Quality') {
     // steps {
       // withSonarQubeEnv('SonarQube') {
        //sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
       // }
     // }
   // }
    stage ('JaCoCo') {
      steps {
      jacoco()
      }
    }
    //stage ('Nexus Upload') {
      //steps {
      //nexusArtifactUploader(
     // nexusVersion: 'nexus3',
      //protocol: 'http',
     // nexusUrl: 'http://ec2-18-223-180-101.us-east-2.compute.amazonaws.com:8081',
      //groupId: 'myGroupId',
      //version: '1.0-SNAPSHOT',
      //repository: 'maven-snapshots',
      //credentialsId: '6df9df59-38c8-440b-b9f7-7c856cd3d981',
      //artifacts: [
      //[artifactId: 'MyWebApp',
            // classifier: '',
      //file: 'MyWebApp/target/MyWebApp.war',
     // type: 'war']
     // ])
     // }
   // }
    stage ('DEV Deploy') {
      steps {
      echo "deploying to DEV Env "
      deploy adapters: [tomcat9(credentialsId: 'b748541d-6d0e-4ff7-b89b-6f04e6310434', path: '', url: 'http://ec2-18-216-135-25.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
      }
    }
    stage ('Slack Notification') {
      steps {
        echo "deployed to DEV Env successfully"
        slackSend(channel:'my-new-slack', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }
        stage ('DEV Approve') {
      steps {
      echo "Taking approval from DEV Manager for QA Deployment"
        timeout(time: 7, unit: 'DAYS') {
        input message: 'Do you want to deploy?', submitter: 'admin'
        }
      }
    }
     stage ('QA Deploy') {
      steps {
        echo "deploying to QA Env "
        deploy adapters: [tomcat9(credentialsId: 'b748541d-6d0e-4ff7-b89b-6f04e6310434', path: '', url: 'http://ec2-18-216-135-25.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
        }
    }
    stage ('QA Approve') {
      steps {
        echo "Taking approval from QA manager"
        timeout(time: 7, unit: 'DAYS') {
        input message: 'Do you want to proceed to PROD?', submitter: 'admin,manager_userid'
        }
      }
    }
    stage ('Slack Notification for QA Deploy') {
      steps {
                echo "deployed to QA Env successfully"
        slackSend(channel:'my-new-slack', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }  
  }
}
