pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials') // Jenkins credentials ID
        DOCKERHUB_REPO = 'karnativ/my-flask-app'
        //SONARQUBE_SERVER = 'SonarQubeServer' // Jenkins SonarQube server name
      //  NEXUS_URL = 'http://nexus.example.com/repository/your-repo/'
      //  NEXUS_CREDENTIALS = credentials('nexus-credentials') // Jenkins credentials ID
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Source') {
            steps {
                git url: 'https://github.com/karnativishnu/devops1.git'
            }
        }

        // stage('SonarQube Code Analysis') {
        //     steps {
        //         withSonarQubeEnv(env.SONARQUBE_SERVER) {
        //             sh 'sonar-scanner -Dsonar.projectKey=flask-app -Dsonar.sources=.'
        //         }
        //     }
        // }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKERHUB_REPO}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Image Scanning') {
            steps {
                sh "trivy image --exit-code 0 --severity HIGH,CRITICAL ${DOCKERHUB_REPO}:${IMAGE_TAG}"
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                    sh "docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}"
                }
            }
        }

        // stage('Archive Artifacts to Nexus') {
        //     steps {
        //         sh "tar czf flask-app-${IMAGE_TAG}.tar.gz ."
        //         sh """
        //         curl -v -u ${NEXUS_CREDENTIALS_USR}:${NEXUS_CREDENTIALS_PSW} \
        //           --upload-file flask-app-${IMAGE_TAG}.tar.gz \
        //           ${NEXUS_URL}flask-app-${IMAGE_TAG}.tar.gz
        //         """
        //     }
        // }
    }

    post {
        always {
            cleanWs()
        }
    }
} 
