
pipeline {
    agent none

    stages 
        { 
	stage('Worker Build') {

	agent {
        docker {
            image 'maven:3,6.1-jdk-8-alpine'
	    args '-v $HOME/.m2:/root/.m2'
        }
        }
		when {
		changeset "**/worker/**"
		}
            steps {
                echo 'Compiling worker app.'
		dir('worker') {
		sh 'mvn compile'
		}
	
	}
            }
        
        
        
         stage('Woker Test') {

	 agent {
        docker {
            image 'maven:3,6.1-jdk-8-alpine'
            args '-v $HOME/.m2:/root/.m2'
        }
        }
		when {
		changeset "**/worker/**"
			}
            steps {
                echo 'Test worker is complete'
		dir('worker') {
                sh 'mvn test'
		}
            }
        }

	stage('Worker Package'){
	agent {
        docker {
            image 'maven:3,6.1-jdk-8-alpine'
            args '-v $HOME/.m2:/root/.m2'
        }
        }
	when {
	branch 'master'
	changeset "**/worker/**"
	}

	steps {
	
	echo 'worker package is performed...'
	dir('worker'){
	sh 'mvn package -DskipTests'
	archiveArtifacts artifact:'**/target/*.jar'

	
	
	}
	}
	
	}


	stage('Worker Docker-package')
        {
         agent any
        when {
                changeset '**/worker/**'
                branch 'master'
                }
        steps {
                script {

                        docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {

                        def customImage = docker.build("harbooshhh/worker:v${env.BUILD_ID}","./worker")

                        /* Push the container to the custom Registry */
                        customImage.push()

                        customImage.push("${env.BRANCH}")

                        }
                }


                }
        }


	
        stage('Result Build') {

        agent {
        docker {
            image 'node:8.16-alpine'
            args '-v $HOME/.m2:/root/.m2'
        }
        }
                when {
                changeset "**/result/**"
                }
            steps {
                echo 'Compiling worker app.'
                dir('result') {
                sh 'npm install'
                }

        }
            }



         stage('Result Test') {

         agent {
        docker {
            image 'node:8.16-alpine'
            args '-v $HOME/.m2:/root/.m2'
        }
        }
                when {
                changeset "**/result/**"
                        }
            steps {
                echo 'Test worker is complete'
                dir('worker') {
		sh 'npm install'
                sh 'npm test'
                }
            }
        }



        stage('result Docker-package')
        {
         agent any
        when {
                changeset '**/result/**'
                branch 'master'
                }
        steps {
                script {

                        docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {

                        def customImage = docker.build("harbooshhh/result:v${env.BUILD_ID}","./result")

                        /* Push the container to the custom Registry */
                        customImage.push()

                        customImage.push("${env.BRANCH}")

                        }
                }


                }
	}




	stage('Vote Build') {

                when {
                changeset "**/vote/**"
                }

                 agent {
                docker {
                image 'python:2.7.18-slim'
		args '--user root'

                        }
                }


            steps {
                echo 'Compiling vote app.'
                dir('vote') {
                sh 'pip  install -r requirements.txt'
                }

        }
            }



         stage('Vote Test') {

                agent {
                docker {
                image 'python:2.7.18-slim'
		args '--user root '

                        }
                }
                when {
                changeset "**/vote/**"
                        }
            steps {
                echo 'Test vote is complete'
                dir('vote') {
		sh 'pip install -r requirements.txt'
                sh 'nosetests -v'
                }
            }
        }


        stage('Vote Docker-package')
        {
         agent any
        when {
                changeset '**/vote/**'
                branch 'master'
                }
        steps {
                script {

                        docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {

                        def customImage = docker.build("harbooshhh/vote:v${env.BUILD_ID}","./vote")

                        /* Push the container to the custom Registry */
                        customImage.push()

                        customImage.push("${env.BRANCH}")

                        }
                }


                }
        }
}
        
    post {
        
        always {
            
            echo 'the job worker is finished.'
        }
    }

}

