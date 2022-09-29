pipeline {
  agent any
  tools {
        maven "maven-3.8.6" 
   }

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' 
            }  
       }
      stage('Test Maven - JUnit') {
            steps {
              sh "mvn test"
            }
            post{
              always{
                junit 'target/surefire-reports/*.xml'
              }
            }
        }
        

        stage('Sonarqube Analysis'){
            steps{
                 withSonarQubeEnv('sonarqube') {
                              sh "mvn sonar:sonar \
                              -Dsonar.projectKey=demo-project \
                              -Dsonar.host.url=http://34.173.61.10:9000" 
                    }
                 }
            }
        stage('Building Docker Image'){
            steps{
                sh '''
                sudo docker build -t nanditasahu/devsecops-demo:$BUILD_NUMBER .
                sudo docker images
                '''
            }
        }
          stage('Image Scanning Trivy'){
            steps{
               sh 'sudo trivy image nanditasahu/devsecops-demo:$BUILD_NUMBER > $WORKSPACE/trivy-image-scan/trivy-image-scan-$BUILD_NUMBER.txt'
               
            }
        }
     }
}
