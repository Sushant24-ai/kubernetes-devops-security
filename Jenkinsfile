pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
       }  
       stage('Jacoco test') {
            steps {
              sh "mvn test"
            }
            post {
              always{
                 junit 'target/surefire-reports/*.xml'
                 jacoco execPattern: 'target/jacoco.exec'
              }
            } 
       }
       stage('Docker Build Image and push') {
            steps {
              withDockerRegistry([credentialsId: "docker-ID", url: ""]){
                    sh 'printenv'
                    sh 'docker build -t sush24/numeric-app:""$GIT_COMMIT"" .'  
                    sh 'docker push sush24/numeric-app:""$GIT_COMMIT""'
              }
            }
       }  
    }
}
