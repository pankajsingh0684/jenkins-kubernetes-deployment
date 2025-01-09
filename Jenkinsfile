pipeline {

  environment {
    DOCKER_IMAGE  = "pankajs2284/react-app"
    IMAGE_TAG = "${env.BUILD_ID}".replaceAll('[^a-zA-Z0-9._-]', '-') // Replace invalid characters
    //IMAGE_TAG = "${env.BUILD_ID}"
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
          echo "Building Docker image: ${DOCKER_IMAGE}:${IMAGE_TAG}"
          bat "docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} ."
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
                      bat "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                      bat "docker push ${DOCKER_IMAGE}:${IMAGE_TAG}"
                        
          }
        }
      }
    }

    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          // kubernetesDeploy(configs: "deployment.yaml", "service.yaml")
           // Replace the Docker image tag in the Kubernetes YAML file
              def yamlFilePath = 'deployment.yaml'
              def file = new File(yamlFilePath)
              def updatedContent = file.text.replaceAll(/image: .*/, "image: ${DOCKER_IMAGE}:${IMAGE_TAG}")
              file.text = updatedContent
                    bat '''
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''
        }
      }
    }

  }

}

