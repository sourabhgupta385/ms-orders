node {
   def SONAR_SCANNER = tool "SONARQUBE_SCANNER"
   def MAVEN_HOME = tool "MAVEN_HOME"
   def JAVA_HOME = tool "JAVA_HOME"

   env.PATH="${env.PATH}:${SONAR_SCANNER}/bin:${MAVEN_HOME}/bin:${JAVA_HOME}/bin"
   sh 'mvn --version'
   
   stage("Checkout Source"){
       checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/akilans/ms-orders.git']]])
   }

   stage("Unit Test & Coverage"){
        sh 'mvn test'
        //publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'coverage', reportFiles: 'index.html', reportName: 'Coverage Report', reportTitles: ''])
   }
   
   
   stage("Code Quality - SonarQube"){
       withSonarQubeEnv {
           sh "sonar-scanner"
        }
   }


   stage("Dev - Building Application"){
        openshiftBuild(buildConfig: 'orders',showBuildLogs: 'true')
   }

   stage("Dev - Deploying Application"){
       openshiftDeploy(deploymentConfig: 'orders')
   }
   
   stage("Verify Application"){
       openshiftVerifyService(svcName: 'orders')
   }
   
   stage("Tagging Image for Production"){
      openshiftTag(srcStream: 'orders', srcTag: 'latest', destStream: 'orders', destTag: 'prod')
   }
   
   /*
   stage("Functional Testing"){
        sh 'python functionalTest.py'   
   }
   
      
   stage("Load Testing"){
         sh 'artillery run perfTest.yml'
         //sh 'artillery run perfTest.yml --output load-test.json && artillery report load-test.json --output load-test-result.html'
   }
   */
 
   /*
   stage("Publish Report"){
      
     
      
      publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'functional-test-result', reportFiles: 'index.html', reportName: 'Functional Test report', reportTitles: ''])
      
      publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '', reportFiles: 'load-test-result.html', reportName: 'Load Test report', reportTitles: ''])
   }
   */
   
   
          
   
   stage('Deploy to Production approval'){
      input "Deploy to prod?"
   }
   /*
   stage("Prod - Building Application"){
        openshiftBuild(namespace:'prod-coe-mern-stack', buildConfig: 'node-backend-app',showBuildLogs: 'true')
   }
   */

   stage("Prod - Deploying Application"){
       openshiftDeploy(namespace:'openshiftplus-prod', deploymentConfig: 'orders')
   }

}