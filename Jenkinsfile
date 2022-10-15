pipeline { 
    environment { 
        repository = "newdeal123/argocd-app"  //docker hub id와 repository 이름
        DOCKERHUB_CREDENTIALS = credentials('docker_credentials') // jenkins에 등록해 놓은 docker hub credentials 이름
        dockerImage = '' 
  }
  agent any 
  stages { 
      stage('Building our image') { 
          steps { 
              script { 
                  // sh "cp /var/lib/jenkins/workspace/sue_jenkins_project/build/libs/sue-member-0.0.1-SNAPSHOT.war /var/lib/jenkins/workspace/pipeline/" // war 파일을 현재 위치로 복사 
                  dockerImage = docker.build repository + ":$BUILD_NUMBER" 
              }
          } 
      }
      stage('Login'){
          steps{
              sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin' // docker hub 로그인
          }
      }
      stage('Deploy our image') { 
          steps { 
              script {
                sh 'docker push $repository:$BUILD_NUMBER' //docker push
              } 
          }
      } 
      stage('Cleaning up') { 
		  steps { 
              sh "docker rmi $repository:$BUILD_NUMBER" // docker image 제거
          }
      } 
  }
    }