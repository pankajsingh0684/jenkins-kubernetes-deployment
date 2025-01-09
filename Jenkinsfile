pipeline {

  environment {
    DOCKER_IMAGE  = "pankajs2284/react-app"
    IMAGE_TAG = "${env.BUILD_ID}"
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/pankajsingh0684/jenkins-kubernetes-deployment.git'
      }
    }

    stage('Build image') {
      steps{
        script {
         // dockerImage = docker.build dockerimagename
          bat 'docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} .'
        }
      }
    }

    stage('Pushing Image') {
      // environment {
      //          registryCredential = 'docker_hub'
      //      }
      steps{
        script {
          // docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
          //   dockerImage.push("latest")
          // Log in to Docker Hub and push the image
                    withCredentials([usernamePassword(credentialsId: 'docker_hub', 
                                                      usernameVariable: 'DOCKER_USER', 
                                                      passwordVariable: 'DOCKER_PASS')]) {
                        bat '''
                            echo "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin
                            docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
                        '''
          }
        }
      }
    }

    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          // kubernetesDeploy(configs: "deployment.yaml", "service.yaml")
           // Replace the Docker image tag in the Kubernetes YAML file
                    bat '''
                        sed -i 's|image: .*$|image: ${DOCKER_IMAGE}:${IMAGE_TAG}|' deployment.yaml
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''
        }
      }
    }

  }

}

