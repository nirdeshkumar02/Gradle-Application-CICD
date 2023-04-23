pipeline{
    agent any

    parameters{
        string(name: 'NexusIp', description: "Provide your public ip of nexus, it will be use for docker", defaultValue: '18.233.162.34')
        string(name: 'NexusRepoPort', description: "Provide your port of docker-nexus-repo, it will be use for docker", defaultValue: '8083')
        string(name: 'AppName', description: "Provide a name to your application", defaultValue: 'gradle-app')
    }

    environment{
        VERSION = "${env.BUILD_ID}"
    }

    stages{
        stage("CheckOut Code: Git"){
            steps{
                script{
                    git branch: 'jenkins_cicd', url: 'https://github.com/nirdeshkumar02/Gradle-Application-CICD.git'
                }
            }
        }
        stage("Sonar Quality Check: SonarQube"){
            steps{
                script{
                    // Install Java 11 on SonarQube Machine
                    withSonarQubeEnv(credentialsId: 'sonar_creds') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube'
                    }
                    timeout(time: 1, unit: 'HOURS'){
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
        stage("Build Docker Image"){
            // SetUp Nexus Repo to Docker to push image to nexus
            // Add Jenkins user to docker group
            steps{
                script{
                    sh "docker build -t ${params.NexusIp}:${params.NexusRepoPort}/${params.AppName}:${VERSION} ."
                }
            }
        }
        stage("Push Image to Nexus"){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'nexus_creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "docker login -u ${USER} -p ${PASS} ${params.NexusIp}:${params.NexusRepoPort}"
                    }
                        sh "docker push ${params.NexusIp}:${params.NexusRepoPort}/${params.AppName}:${VERSION}"
                        sh "docker rmi ${params.NexusIp}:${params.NexusRepoPort}/${params.AppName}:${VERSION}"
                }
            }
            // configure gmail to jenkins and then use gmail to send update over email
            post{
                always{
                    mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "hackingstudio.tcp@gmail.com";
                }
            }
        }
    }
}