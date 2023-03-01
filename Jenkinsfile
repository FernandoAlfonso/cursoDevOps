// Jenkinsfile para comunicar jenkins con git
pipeline {
	environment {
		// dockerImageNameMySql = "fernandofar/mysql:v1"
		dockerImageNamePhpMyAdmin = "fernandofar/phpmyadmin:v1"
		dockerImageNameApp = "fernandofar/app:v1"
		// dockerImageMySql = ""
		dockerImagePhpMyAdmin = ""
		dockerImageApp = ""
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

			// mysql
				// stage ('Construir la imagen de MySql') {
				// 	steps {
				// 		dir ('mysql') {
				// 			script {
				// 				dockerImageMySql = docker.build dockerImageNameMySql
				// 			}
				// 		}
				// 	}
				// }

				// stage ('Subir la imagen MySql') {
				// 	steps {
				// 		dir ('mysql') {
				// 			script {
				// 				docker.withRegistry ('https://registry.hub.docker.com', registryCredential) {
				// 					dockerImageMySql.push ('v1') // tag
				// 				}
				// 			}
				// 		}
				// 	}
				// }

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

			// Ejecutar POD (kubernetes)
				stage ('Ejecutar POD') {
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

							// mysql
								sh 'cd mysql && scp -r -o StrictHostKeyChecking=no deployment-service-mysql-poncho.yaml digesetuser@148.213.1.131:/home/digesetuser/'
								script {
									try {
										sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment-service-mysql-poncho.yaml --kubeconfig=/home/digesetuser/.kube/config'
	                  sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment podmysqlponcho2 -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'
	                  // sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment podmysqlponcho2 -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'

									} catch (error) {}
								}

							// phpmyadmin
								sh 'cd phpmyadmin && scp -r -o StrictHostKeyChecking=no deployment-service-phpmyadmin-poncho.yaml digesetuser@148.213.1.131:/home/digesetuser/'
								script {
									try {
										sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment-service-phpmyadmin-poncho.yaml --kubeconfig=/home/digesetuser/.kube/config'
	                  sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment podphpmyadminponcho2 -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'
	                  // sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment podphpmyadminponcho2 -n cursokubernetesponcho2 --kubeconfig=/home/digesetuser/.kube/config'

									} catch (error) {}
								}
						}
					}
				}
		}
}

