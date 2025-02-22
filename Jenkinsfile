pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: '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', 
                url: 'https://github.com/nkummuru/cicd-end-to-end',
                branch: 'master'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t nkummuru/django-todo-web:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
		    withCredentials([usernamePassword(credentialsId: 'docker-cred', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh 'docker login -u "$DOCKER_USERNAME" -p "$DOCKER_PASSWORD"'
                        sh 'docker push nkummuru/django-todo-web:${BUILD_NUMBER}'
                    }
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: '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', 
                url: 'https://github.com/nkummuru/cicd-demo-manifests-repo.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'git-cred', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        #sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
			git remote set-url origin "https://$GIT_USERNAME:$GIT_PASSWORD@github.com/nkummuru/cicd-demo-manifests-repo.git"
                        git push https://github.com/nkummuru/cicd-demo-manifests-repo.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
