pipeline { 
    environment { 
        repository = "newdeal123/argocd-app"  //docker hub id와 repository 이름
        DOCKERHUB_CREDENTIALS = credentials('docker_credentials') // jenkins에 등록해 놓은 docker hub credentials 이름
        dockerImage = ''
        branchName = "${env.BRANCH_NAME == 'main' ? 'staging' : env.BRANCH_NAME}"
  }
  agent any 
  stages { 
      stage('Building our image') { 
          steps { 
              script { 
                  // sh "cp /var/lib/jenkins/workspace/sue_jenkins_project/build/libs/sue-member-0.0.1-SNAPSHOT.war /var/lib/jenkins/workspace/pipeline/" // war 파일을 현재 위치로 복사 
                  dockerImage = docker.build repository + ":$BUILD_NUMBER" 
                  dockerImage = docker.build repository + ":latest" 
              }
          } 
      }
      stage('Login'){
          steps{
            sh 'echo $branchName env.BRANCH_NAME'
            sh "echo $branchName env.BRANCH_NAME"
              sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin' // docker hub 로그인
          }
      }
      stage('Deploy docker image') { 
          steps { 
              script {
                sh 'docker push $repository:$BUILD_NUMBER' //docker push
                sh 'docker push $repository:latest'
              } 
          }
      } 
      stage('Kubernetes Manifest Update') {
        steps {
            git credentialsId: 'argoCD-app-config-credential',
                url: 'https://github.com/newdeal123/argoCD-app-config.git',
                branch: 'main'

            sh "sed -i 's/argocd-app:.*\$/argocd-app:${currentBuild.number}/g' ./$branchName/deployment.yaml"
            sh "git add ./$branchName/deployment.yaml"
            sh "git commit -m '[UPDATE] argoCD-app ${currentBuild.number} image versioning'"
            withCredentials([usernamePassword(credentialsId: 'argoCD-app-config-credential', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                sh "git remote set-url origin git@github.com:newdeal123/argoCD-app-config.git"
                sh "git push -u origin main"
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