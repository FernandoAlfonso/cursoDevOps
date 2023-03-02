// Jenkinsfile para comunicar jenkins con git
pipeline {
	environment {
		dockerImageNameApp = "fernandofar/app:v1"
		dockerImageApp = ""
		dockerImageNamePhpMyAdmin = "udecdj2022/phpmyadmin"
		dockerImagePhpMyAdmin = ""
		registryCredential = 'poncho_curso_docker_token' // nombre de variable por default
	}
	
	agent any
		stages {
			stage ('Revisar código') {
				steps {
					git credentialsId: 'poncho_curso_devops', // nombre de variable por default
					url: 'https://github.com/FernandoAlfonso/cursoDevOps.git',
					branch: 'main'
				}
			}

			// app
				stage ('Construir la imagen de la aplicación') {
					steps {
						dir ('app') {
							script {
								dockerImageApp = docker.build dockerImageNameApp
							}
						}
					}
				}

				stage ('Subir la imagen App') {
					steps {
						dir ('app') {
							script {
								docker.withRegistry ('https://registry.hub.docker.com', registryCredential) {
									dockerImageApp.push ('v1') // tag
								}
							}
						}
					}
				}

			// PhpMyAdmin
				stage ('Construir la imagen de PhpMyAdmin') {
					steps {
						dir ('phpmyadmin') {
							script {
								dockerImageNamePhpMyAdmin = docker.build dockerImageNamePhpMyAdmin
							}
						}
					}
				}

				stage ('Subir la imagen PhpMyAdmin') {
					steps {
						dir ('phpmyadmin') {
							script {
								docker.withRegistry ('https://registry.hub.docker.com', registryCredential) {
									dockerImageNamePhpMyAdmin.push ('v1') // tag
								}
							}
						}
					}
				}

			
			// Ejecutar PODs (kubernetes) 
				stage ('Ejecutar POD App') {
					steps {
						sshagent (['sshsanchez']) {
							// app
								sh 'cd app && scp -r -o StrictHostKeyChecking=no deployment-service-app-poncho.yaml digesetuser@148.213.1.131:/home/digesetuser/'
								script {
									try {
										sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment-service-app-poncho.yaml --kubeconfig=/home/digesetuser/.kube/config'
	                  sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment podappponcho -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'
	                  // sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment podappponcho -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'

									} catch (error) {}
								}
						}
					}
				}

				stage ('Ejecutar POD MySQL') {
					steps {
						sshagent (['sshsanchez']) {
							// mysql
								sh 'cd mysql && scp -r -o StrictHostKeyChecking=no deployment-service-mysql-poncho.yaml digesetuser@148.213.1.131:/home/digesetuser/'
								script {
									try {
										sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment-service-mysql-poncho.yaml --kubeconfig=/home/digesetuser/.kube/config'
	                  sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment mysql-deployment -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'
	                  // sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment mysql-deployment -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'

									} catch (error) {}
								}
						}
					}
				}

				stage ('Ejecutar POD PhpMyAdmin') {
					steps {
						sshagent (['sshsanchez']) {
							// phpmyadmin
								sh 'cd phpmyadmin && scp -r -o StrictHostKeyChecking=no deployment-service-phpmyadmin-poncho.yaml digesetuser@148.213.1.131:/home/digesetuser/'
								script {
									try {
										sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment-service-phpmyadmin-poncho.yaml --kubeconfig=/home/digesetuser/.kube/config'
	                  sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment phpmyadmin-deployment -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'
	                  // sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment phpmyadmin-deployment -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'

									} catch (error) {}
								}
						}
					}
				}
		}

	// post {
	// 	success {
	// 		slackSend channel: 'prueba_pipeline_poncho', color: 'good', failOnError: true, message: "${custom_msg()}", teamDomain: 'universidadde-bea3869', tokenCredentialId: 'slackpass'
	// 	}
	// }
}

// def custom_msg() {
//   def JENKINS_URL= "jarvis.ucol.mx:8080"
//   def JOB_NAME = env.JOB_NAME
//   def BUILD_ID= env.BUILD_ID
//   def JENKINS_LOG= " DEPLOY LOG: Job [${env.JOB_NAME}] Logs path: ${JENKINS_URL}/job/${JOB_NAME}/${BUILD_ID}/consoleText"
//   return JENKINS_LOG
// }