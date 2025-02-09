pipeline {
    agent { label 'jappbuildserver1' }    

    tools {
        maven "maven_3.6.3"
    }

    environment {    
        DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
    } 
    
    stages {
        stage('SCM Checkout') {
            steps {
                git 'https://github.com/dhotekomal71/star-agile-insurance-project.git'
            }
        }
        stage('Maven Build') {
            steps {
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage("Docker Build") {
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
                    sshPublisher(publishers: [
                        sshPublisherDesc(
                            configName: 'SA-FEB09-AController', 
                            transfers: [
                                sshTransfer(
                                    cleanRemote: false,
                                    execCommand: 'ansible-playbook ansible-playbook.yml',
                                    execTimeout: 120000,
                                    remoteDirectory: '.',
                                    sourceFiles: 'ansible-playbook.yml'
                                )
                            ]
                        )
                    ])
                }
            }
        }
        stage('Delete Ansible Playbook') {  // New Stage Added Here
            steps {
                script {
                    sshPublisher(publishers: [
                        sshPublisherDesc(
                            configName: 'SA-FEB09-AController',
                            transfers: [
                                sshTransfer(
                                    execCommand: 'rm -f /home/devopsadmin/ansible-playbook.yml'
                                )
                            ]
                        )
                    ])
                }
            }
        }
    }
}
