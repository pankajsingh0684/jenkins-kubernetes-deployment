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
              // Read the YAML file
                    def yamlContent = readFile('deployment.yaml')

                    // Replace the image tag
                    def updatedYamlContent = yamlContent.replaceAll(/image: .*/, "image: ${DOCKER_IMAGE}:${IMAGE_TAG}")
                     echo "updatedYamlContent: ${updatedYamlContent}"
                    // Write the updated content back to the file
                    writeFile(file: 'deployment.yaml', text: updatedYamlContent)
                    bat '''
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''
        }
      }
    }

  }

}

