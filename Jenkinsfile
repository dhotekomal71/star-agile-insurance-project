pipeline {
    agent { label 'jappbuildserver1' }	

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven_3.6.3"
    }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	} 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/dhotekomal71/star-agile-insurance-project.git'
                //git 'https://github.com/LoksaiETA/Java-mvn-app2.git'
            }
		}
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}
       stage("Docker build"){
            steps {
				sh 'docker version'
				sh "docker build -t komaldhote123/insuranceapp-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag komaldhote123/insuranceapp-app:${BUILD_NUMBER} komaldhote123/insuranceapp-app:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Push2DockerHub') {

			steps {
				sh "docker push komaldhote123/insuranceapp-app:latest"
			}
		}
        stage('Deploy to Ansible Dev Environment') {
            steps {
		script {
		sshPublisher(publishers: [sshPublisherDesc(configName: 'SA-FEB09-AController', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook ansible-playbook.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'ansible-playbook.yml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		       }
            }
    	}
    }
}
