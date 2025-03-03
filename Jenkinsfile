pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
               git branch: 'main', 
               credentialsId: 'cdf2b1ac-964c-43a1-a542-52030e361ca4', 
               url: 'https://github.com/iam-sameera/cicd-end-to-end'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t devopswithsam/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push devopswithsam/cicd-e2e:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git branch: 'main', 
                credentialsId: 'cdf2b1ac-964c-43a1-a542-52030e361ca4', 
                url: 'https://github.com/iam-sameera/cicd-demo-manifests-repo.git'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'cdf2b1ac-964c-43a1-a542-52030e361ca4', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                sh '''
                cat deploy.yaml
                sed -i "s|devopswithsam/cicd-e2e:[0-9]*|devopswithsam/cicd-e2e:${BUILD_NUMBER}|g" deploy.yaml
                cat deploy.yaml
                git config --global credential.username "$GIT_USERNAME"
                git config --global credential.helper '!echo "$GIT_PASSWORD"'
                git add deploy.yaml
                git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                git remote -v
       
                '''                          
                    }
                }
            }
        }
    }
}
