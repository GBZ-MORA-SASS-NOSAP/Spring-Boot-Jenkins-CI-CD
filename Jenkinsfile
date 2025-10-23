pipeline {
    agent any

    tools{
        jdk 'jdk17'
        maven 'maven3'
    }

    stages {
        stage('Code Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/GBZ-MORA-SASS-NOSAP/Spring-Boot-Jenkins-CI-CD.git'
            }
        }
        /*
        stage('Sonarqube Analysis') {
            steps {
                sh ''' mvn clean verify sonar:sonar \
                    -Dsonar.host.url=http://161.132.41.100:9000/ \
                    -Dsonar.login=squ_07ee9fdc11ad5d229fa448ceb6e09a84a4f1ca17 '''
            }
        }
        */ 
        stage('Clean & Package'){
            steps{
                sh "mvn clean package -DskipTests"
            }
        }


        
       stage("Docker Build & Push"){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'DockerHub-Token', toolName: 'docker') {
                        def imageName = "spring-boot-prof-management"
                        def buildTag = "${imageName}:${BUILD_NUMBER}"
                        def latestTag = "${imageName}:latest"  // Define latest tag
                        
                        sh "docker build -t ${imageName} -f Dockerfile.final ."
                        sh "docker tag ${imageName} abdeod/${buildTag}"
                        sh "docker tag ${imageName} abdeod/${latestTag}"  // Tag with latest
                        sh "docker push abdeod/${buildTag}"
                        sh "docker push abdeod/${latestTag}"  // Push latest tag
                        env.BUILD_TAG = buildTag
                    }
                        
                }
            }
        }
        
        stage('Vulnerability scanning'){
            steps{
                sh " trivy image abdeod/${buildTag}"
            }
        }

        stage("Staging"){
            steps{
                sh 'docker-compose up -d'
            }
        }
    }
}
