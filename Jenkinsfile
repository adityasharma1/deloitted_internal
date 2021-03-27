def projectId = "deloitte-demo-308622"

pipeline {
   agent any

   stages {
        stage('Stage 1 - workspace and versions') {
                        
            steps {
                echo '****************************** Stage 1'
                sh 'echo $WORKSPACE'
                //sh 'docker --version'
                sh 'gcloud version'
                sh 'nodejs -v'
                sh 'npm -v'
            }
        }
        
        stage('Stage 2 - build internal') {
            steps {
                echo '****************************** Stage 3'
                dir("${env.WORKSPACE}/internal"){
                  echo 'Retrieving source from github' 
                    git branch: 'master',
                        url: 'https://github.com/adityasharma1/deloitted_internal.git'
                    sh "ls -a"
                    echo 'install dependencies' 
                    sh 'npm install'
                    echo 'Run tests'
                    sh 'npm test'
                    echo 'Tests passed on to build Docker container'
                    echo "build id = ${env.BUILD_ID}"
                    sh "gcloud builds submit -t gcr.io/${projectId}/internal-image:v2.${env.BUILD_ID} ."
                }
            }
        }
        stage('Stage 3') {
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials demo-events-feed-cluster --zone us-central1-a --project deloitte-demo-308622'
                echo 'Update the image'
                echo "gcr.io/deloitte-demo-308622/internal:2.${env.BUILD_ID}"
                sh "kubectl set image deployment/events-internal events-external=gcr.io/deloitte-demo-308622/internal-image:v2.${env.BUILD_ID} --record"
            }
        }

   }
}