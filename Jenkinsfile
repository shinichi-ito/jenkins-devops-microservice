//SCRIPTED

//DECLARATIVE
pipeline {
	agent any
	//agent { docker { image 'maven:3.6.3'} }
	//agent { docker { image 'node:13.8'} }
	environment {
		dockerHome = tool 'myDocker'
		mavenHome = tool 'myMaven'
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"
	}

	stages {
		stage('Checkout') {
			steps {
				sh 'mvn --version'
				sh 'docker version'
				echo "Build"
				echo "PATH - $PATH"
				echo "BUILD_NUMBER - $env.BUILD_NUMBER"
				echo "BUOLD_ID - $env.BUILD_ID"
				echo "JOB_NAME - $env.JOB_NAME"
				echo "BUILD_TAG - $env.BUILD_TAG"
				echo "BUILD_URL - $env.BUILD_URL"
			}		
		}

		stage('Compile') {//人間言語から　機械言語に変更する
			steps {
				sh "mvn clean compile"
			}
		}

		stage('Test') {//Testする
			steps {
				sh "mvn test"
			}		
		}

		stage('Integration Test') {//結合テストを行う
			steps {
				sh "mvn failsafe:integration-test failsafe:verify"
			}		
		}

		stage('Package') {//パッケージング testはのぞく
			steps {
				sh "mvn package -DskipTests"
			}		
		}

		stage('Build Docker Image') {
			steps {
				//"docker build -t lotsofjoy/devops:$env.BUILD_TAG"
				script {
					dockerImage = docker.build("lotsofjoy/devops:$env.BUILD_TAG")
				}
			}
		}

		stage('Push Docker Image') {
			steps {
				script {
					docker.withRegistry('', 'dockerhub') {
						dockerImage.push();
						dockerImage.push('latest');
					}	
				}
			}
		}
	} 

	post {
		always {
			echo 'Im awesome. I run always'
		}
		success {
			echo 'I run when you are successful'
		}
		failure {
			echo 'I run when you fail'
		}
	}
}
