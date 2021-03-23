pipeline {
    agent any
    stages {
        stage('Building the Student Survey Image') {
            steps {
                script {
                    checkout scm
		    sh 'rm -rf *.war'
		    sh 'jar -cvf StudentSurvey.war -C WebContent/ .'
                    sh 'echo ${BUILD_TIMESTAMP}'
                    withCredentials([usernamePassword(credentialsId: 'docker-pass', passwordVariable: 'password', usernameVariable: 'username')]){
		        sh "docker login -u ${username} -p ${password}"
		    }
		    sh "docker build -t liyuqin33/studentsurvey645:${BUILD_TIMESTAMP} ."
                    //def customImage = docker.build("liyuqin33/studentsurvey645:${BUILD_TIMESTAMP}");
                }
            }
        }
        stage('Pushing Image to DockerHub') {
            steps {
                script {
                    sh 'docker push liyuqin33/studentsurvey645:${BUILD_TIMESTAMP}'
                }
            }
        }
        stage('Deloying to Rancher as single od') {
            steps {
                sh 'kubectl set image deployment/student-survey student-survey=liyuqin33/studentsurvey645:${BUILD_TIMESTAMP} -n swe645-hw2'
            }
        }
        stage('Deploying to Rancher as with load balancer') {
            steps {
                sh 'kubectl set image deployment/student-survey-loadbalancer student-survey-loadbalancer=liyuqin33/studentsurvey645:${BUILD_TIMESTAMP} -n swe645-hw2'
            }
        }
    }
}
