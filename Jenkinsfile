
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
    APP_GITOPS_REPO_URL = 'https://github.com/your-username/gitops-stocksanalyzer-system.git' 
    APP_GITOPS_BRANCH = 'main'
    //APP_GITOPS_CREDENTIALS_ID = 'cred-github-fedora800-PAT'

    //APP_NAME = "stocksanalyzer-frontend-app"
    //APP_VERSION_PREFIX = "1.0"            // currently hardcoding till i find solution to maybe get from build config or somewhere else
    //APP_VERSION = "${APP_VERSION_PREFIX}.${env.BUILD_NUMBER}"      // Concatenate using Groovy string interpolation

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
            //git branch: 'main', url: 'https://github.com/fedora800/scratch_project.git'
            git branch: APP_GIT_REPO_BRANCH, credentialsId: APP_GIT_REPO_CREDENTIALS_ID, url: APP_GIT_REPO_URL
          }
          catch (err) {
            echo err
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



