// Jenkinsfile para comunicar jenkins con git
pipeline {
	enviroment {
		dockerImageName = "fernandofar/curso:devops"
		dockerImage1 = ""
	}

	agent any { 
		stages {
			stage ('Revisar código') {
				steps {
					git credentialsId: 'poncho-cursos-devops',
					url: 'https://github.com/FernandoAlfonso/cursoDevOps.git',
					branch: 'main'
				}
			}
		}
	}
}

