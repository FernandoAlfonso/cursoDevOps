// Jenkinsfile para comunicar jenkins con git
pipeline {
	environment {
		dockerImageName = "fernandofar/curso:devops"
		dockerImage1 = ""
	}
	
	agent any
		stages {
			stage ('Revisar código') {
				steps {
					git credentialsId: 'poncho-curso-devops',
					url: 'https://github.com/FernandoAlfonso/cursoDevOps.git',
					branch: 'main'
				}
			}
		}
}

