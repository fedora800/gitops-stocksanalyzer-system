
//-------------------- section : Functions --------------------

// function to print the stage name more clearly in the jenkins console output, can be called within each stage
def PrintStageName() {
  def currentTime = sh(script: "date +'%T'", returnStdout: true).trim()
  // Print the stage name and current time
  echo "-----------------STAGE: ${env.STAGE_NAME ?: 'Unknown Stage'}-----------------${currentTime}---------"
}


pipeline {
    agent any
    
  environment {
    APP_GITOPS_REPO_NAME = "gitops-stocksanalyzer-system"
    APP_GITOPS_REPO_URL = 'https://github.com/fedora800/gitops-stocksanalyzer-system.git' 
    APP_GITOPS_BRANCH = 'main'
    APP_GITOPS_CREDENTIALS_ID = 'cred-github-fedora800-PAT'

    K8S_NAMESPACE = 'ns-stocksanalyzer'
    K8S_DEPLOYMENT_FILE = 'kubernetes-manifests/frontend/dpl-frontend.yaml'
    K8S_SERVICES_FILE = 'kubernetes-manifests/frontend/svc-frontend.yaml'
  }
    
  stages {

    stage('Initialization') {
        steps {
            script {
                PrintStageName()  // Print the name of the current stage
            }

            // Step 1: Get and print the Job Time
            script {
                def jobTime = sh(script: "date '+%F %T'", returnStdout: true).trim()
                env.JOB_TIME = jobTime  // Store job time in the environment variable
                echo "Job Time: ${env.JOB_TIME}"
            }

            // Step 2: Clean the Jenkins Workspace
            script {
                cleanWs()  // Clean the Jenkins workspace
                echo "Workspace cleaned successfully."
            }
        }
    }


    stage('Pull Code from git PUBLIC repo')  {
      steps {
        PrintStageName()
        script {
          try {
            // Pull code from a GitHub repository
            echo "Cloning repository ${APP_GITOPS_REPO_NAME} from branch ${APP_GITOPS_BRANCH}"
            //git branch: APP_GITOPS_REPO_BRANCH, credentialsId: APP_GITOPS_REPO_CREDENTIALS_ID, url: APP_GITOPS_REPO_URL
            git branch: 'main', url: 'https://github.com/fedora800/gitops-stocksanalyzer-system.git' 
          }
          catch (err) {
            echo err
          }
        }
      }
    }


    stage('Connect to Kubernetes cluster using config file credentials and lookup resources') {
      steps {
        PrintStageName()
        script {
          // Wrap with the credentials block to use the kubeconfig from Jenkins credentials into Jenkins env variable
          withCredentials([file(credentialsId: 'cred-kubernetes-config-file', variable: 'KUBECONFIG')]) {
            // Optionally check the cluster details
            sh 'kubectl config view'
            // Run your kubectl commands, for example, deploying an application
            sh 'kubectl get nodes -o wide'
            // Example of deploying a YAML manifest
            //sh 'kubectl apply -f deployment.yaml'
            // You can also run other kubectl commands as needed
            sh 'kubectl get pods -n ${K8S_NAMESPACE} -o wide'
          }
        }
      }
    }



    stage('Connect to Kubernetes cluster using config file credentials and APPLY manifest changes') {
      steps {
        PrintStageName()
        script {
          // Wrap with the credentials block to use the kubeconfig from Jenkins credentials into Jenkins env variable
          withCredentials([file(credentialsId: 'cred-kubernetes-config-file', variable: 'KUBECONFIG')]) {
            sh 'kubectl apply -f $K8S_DEPLOYMENT_FILE'
            sh 'kubectl apply -f $K8S_SERVICES_FILE'
            sh 'echo Waiting for 10 seconds for resource to come up...; sleep 10'
            sh 'kubectl -n ${K8S_NAMESPACE} get all --show-labels'

          }
        }
      }
    }





  } // end-stages

  post {
      always {
          // Always run steps after the pipeline completes
          echo 'Pipeline finished.'
          cleanWs() // Clean workspace
      }
      success {
          // Run steps after a successful build
          echo 'Build succeeded!'
      }
      failure {
          // Run steps after a failed build
          echo 'Build failed!'
      }
  } // end-post


} // end-pipeline



