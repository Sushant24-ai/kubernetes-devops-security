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
           
       }
       stage('Mutation Tests - PIT') {
             steps {
                sh "mvn org.pitest:pitest-maven:mutationCoverage"
            }
      }

      stage('SonarQube - SAST') {
             steps {
              // withSonarQubeEnv('SonarQube') {
                   sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://20.163.155.184:9000 -Dsonar.login=sqp_7a61860024c273826d90f937ca74f1c70ef3c0e8"
            }
          //   timeout(time: 2, unit: 'MINUTES') {
          //        script {
          //          waitForQualityGate abortPipeline: false
          //      }
          //  }
        // }
      }

         stage('Vulnerability Scan - Docker') {
              steps {
                 parallel(
                       	"Dependency Scan": {
        	                	sh "mvn dependency-check:check"
		                 	},
		                  	"Trivy Scan":{
			                  	  sh "bash trivy-docker-image-scan.sh"
		                	},
		                  // 	"OPA Conftest":{
			                //     	sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-docker-security.rego Dockerfile'
		                	// }   	
              	)
              }
         }
      
      //  stage('Docker Build Image and push') {
      //       steps {
      //         withDockerRegistry([credentialsId: "docker-ID", url: ""]){
      //               sh 'printenv'
      //               sh 'docker build -t sush24/numeric-app:""$GIT_COMMIT"" .'  
      //               sh 'docker push sush24/numeric-app:""$GIT_COMMIT""'
      //         }
      //       }
      //  } 
    
      //  stage('Kubernetes deployment - DEV') {
      //       steps {
      //         withKubeConfig([credentialsId: 'kube-config']){
      //             sh "sed -i 's#REPLACE_ME#docker-registry:5000/java-app:latest#g' k8s_deployment_service.yaml"
      //             sh 'kubectl apply -f k8s_deployment_service.yaml'
      //         }
      //       }
      //  }  
    post { 
        always { 
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
          pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
          dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
          publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'owasp-zap-report', reportFiles: 'zap_report.html', reportName: 'OWASP ZAP HTML Report', reportTitles: 'OWASP ZAP HTML Report'])
        
 		  //Use sendNotifications.groovy from shared library and provide current build result as parameter 
          //sendNotification currentBuild.result
        }
    }
    
  }
