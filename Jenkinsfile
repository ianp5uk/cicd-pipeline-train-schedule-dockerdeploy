pipeline {
    agent any
	parameters {
		string(name: 'prod_ip',
			defaultValue: '10.192.10.20',
			description: 'ip address of host server to deploy container to')
	}
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build("ianp5uk/train-schedule")
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy To Prod') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Prod?'
                milestone(1)
                withCredentials([sshUserPrivateKey(credentialsId: 'swarm_login', keyFileVariable: 'KEYFILE', passphraseVariable: '', usernameVariable: 'USERNAME')]) {
                    script {
                        sh "sshpass -v ssh -i $KEYFILE -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull ianp5uk/train-schedule:${env.BUILD_NUMBER}\""
                        try {
                            sh "sshpass -v ssh -i $KEYFILE -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker stop train-schedule\""
                            sh "sshpass -v ssh -i $KEYFILE -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rm train-schedule\""
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "sshpass -v ssh -i $KEYFILE -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker run --restart always --name train-schedule -p 80:3000 -d ianp5uk/train-schedule:${env.BUILD_NUMBER}\""
                    }
                }
            }
        }
		stage('Smoke Test') {
			steps {
				script {
						sh "sleep 10"
						sh "curl -s -o /dev/null -D - 0.0.0.0:80"
					}
				}
			}
		
	
    }
}
