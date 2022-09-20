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
       stage('Mutation Tests - PIT') {
             steps {
                sh "mvn org.pitest:pitest-maven:mutationCoverage"
            }
      }
      
//        stage('Docker Build Image and push') {
//             steps {
//               withDockerRegistry([credentialsId: "docker-ID", url: ""]){
//                     sh 'printenv'
//                     sh 'docker build -t sush24/numeric-app:""$GIT_COMMIT"" .'  
//                     sh 'docker push sush24/numeric-app:""$GIT_COMMIT""'
//               }
//             }
//        } 
    
//        stage('Kubernetes deployment - DEV') {
//             steps {
//               withKubeConfig([credentialsId: 'kube-config']){
//                // sh "sed -i 's#replace#siddharth67/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
//                     sh "kubectl apply -f k8s_deployment_service.yaml"  
//               }
//             }
//        }  
      }
  }
