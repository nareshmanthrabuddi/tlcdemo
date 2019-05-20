node {
   def mvnHome
    properties([
     parameters([
       choiceParam(
         choices: 'development\nSIT\nproduction',
            description: 'Please select the Build ENVIRONMENTS',
            name: 'ENVIRONMENT'
       )
     ])
   ])
   stage('Check out the code from Git Repository') { // for display purposes
      git credentialsId: '5ea37bf2-e9f9-4358-89d1-8b246130f034', url: 'https://github.com/nareshmanthrabuddi/devopsdemo.git'
   }
   stage('Clean') {
      // Run the maven build
      if (isUnix()) {
         echo "This is linux box"
         sh 'mvn clean'
      } else {
            echo "This is windows machine"
            bat 'mvn clean'
      }
   }
   stage('Unit Testing and packaging') {
      // Run the maven build
      if (isUnix()) {
         sh 'mvn package'
      } else {
        bat 'mvn package'
      }
   }
    stage('SONAR Analysis') {
      // Run the maven build
      if (isUnix()) {
         sh 'mvn sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.login=ec0d1390b33ce434b483e2c0d43c6be2e36cfcf9'
      } else {
         bat 'mvn sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.login=ec0d1390b33ce434b483e2c0d43c6be2e36cfcf9'
      }
   }
   stage('Deploy') {
      if (isUnix()) {
         echo "Deployment Started"
            withCredentials([usernamePassword(credentialsId: 'c9cb559a-7c9a-43ce-b1bc-d8aa2dfef0bb', passwordVariable: 'ANYPOINT_PASSWORD', usernameVariable: 'ANYPOINT_USERNAME')]) {
                sh 'mvn deploy -DmuleDeploy -Denv=$ENVIRONMENT -Danypoint.username=${ANYPOINT_USERNAME} -Danypoint.password=${ANYPOINT_PASSWORD}'
            }
      } else {
            echo "Deployment Started"
            withCredentials([usernamePassword(credentialsId: 'c9cb559a-7c9a-43ce-b1bc-d8aa2dfef0bb', passwordVariable: 'ANYPOINT_PASSWORD', usernameVariable: 'ANYPOINT_USERNAME')]) {
                bat "mvn deploy -DmuleDeploy -Denv=$ENVIRONMENT -Danypoint.username=${ANYPOINT_USERNAME} -Danypoint.password=${ANYPOINT_PASSWORD}"
            }
      }
   }
   if (currentBuild.currentResult == 'SUCCESS') {
        stage('Success') {
            echo "Current build status is success::"+currentBuild.currentResult
            emailext (
                subject: "Success: Job '${env.JOB_NAME} ${env.BUILD_NUMBER}'",
                body: "Check console output at '${env.BUILD_URL}' of '${env.JOB_NAME}'",
                to: "naresh.manthrabuddi@whishworks.com",
                from: "jenkins@whishworks.com"
            )
        }
    } else {
        stage('Failed') {
            echo "Curretn build status is Failed"
            emailext (
                subject: "Failure: Job '${env.JOB_NAME} ${env.BUILD_NUMBER}'",
                body: "Check console output at '${env.BUILD_URL}' of '${env.JOB_NAME}'",
                to: "naresh.manthrabuddi@whishworks.com",
                from: "jenkins@whishworks.com"
            )
        }
    }
}