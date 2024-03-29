pipeline {
    agent any
     tools {
        // Install the Maven version configured as "M2" and add it to the path.
        maven 'M2_HOME'
    }

	//environment {	
	//	DOCKERHUB_CREDENTIALS=credentials('docker-login-credentials')
	//} 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                // git 'https://github.com/poojachede/projectrepo'
		        git branch: 'main', url: 'https://github.com/poojachede/projectrepo.git'
            }
		}
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}
        stage("Docker build") {
            steps {
                sh 'docker version'
                sh "docker build -t poojachede/bankapp-eta-app:${BUILD_NUMBER} ."
                sh 'docker image list'
                sh "docker tag poojachede/bankapp-eta-app:${BUILD_NUMBER} poojachede/bankapp-eta-app:latest"
            }
        }
        stage('Login2DockerHub and Push the Image') {
    steps {
        script {
            // Retrieve Docker Hub credentials from Jenkins credentials
          //  withCredentials([usernamePassword(credentialsId: 'docker-login-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                withCredentials([usernameColonPassword(credentialsId: 'docker-login-credentials', variable: 'docker-login-credentials')]) {
    // some block
}
		echo "Docker Hub Username: $DOCKER_USERNAME"
                echo "Docker Hub Password: $DOCKER_PASSWORD"
                
                sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
		sh "docker push poojachede/bankapp-eta-app:latest"
            }
        }
    }
}
		
		stage('Deploy to Kubernetes') {
            steps {
                script {
                    kubeconfig(credentialsId: 'k8s', serverUrl: '') {
                        sh 'kubectl apply -f kubernetesdeploy.yaml'
                    } 
                }
            }
        }
    }
}
