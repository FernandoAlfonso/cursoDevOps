// Jenkinsfile para comunicar jenkins con git
pipeline {
	environment {
		dockerImageNameMySql = "fernandofar/mysql:v1"
		dockerImageNamePhpMyAdmin = "fernandofar/phpmyadmin:v1"
		dockerImageNameApp = "fernandofar/app:v1"
		dockerImageMySql = ""
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
				stage ('Construir la imagen de MySql') {
					steps {
						dir ('mysql') {
							script {
								dockerImageMySql = docker.build dockerImageNameMySql
							}
						}
					}
				}

				stage ('Subir la imagen MySql') {
					steps {
						dir ('mysql') {
							script {
								docker.withRegistry ('https://registry.hub.docker.com', registryCredential) {
									dockerImageMySql.push ('v1') // tag
								}
							}
						}
					}
				}

			// PhpMyAdmin
				stage ('Construir la imagen de PhpMyAdmin') {
					steps {
						dir ('mysql') {
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
						sshagent (['rodriguezssh']) {
							sh 'cd app && scp -r -o StrictHostKeyChecking=no deployment_service.yaml digesetuser@148.213.1.131:/home/digesetuser/'
							script {
								try {
									sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl apply -f deployment_service.yaml --kubeconfig=/home/digesetuser/.kube/config'
									sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout restart deployment app_kubernetes_poncho --kubeconfig=/home/digesetuser/.kube/config'
									sh 'ssh digesetuser@148.213.1.131 microk8s.kubectl rollout status deployment app_kubernetes_poncho --kubeconfig=/home/digesetuser/.kube/config'          
								} catch (error) {

								}
							}
						}
					}
				}
		}
}

