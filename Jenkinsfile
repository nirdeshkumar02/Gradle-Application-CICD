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
        stage("Build Docker Image and Push to Nexus: Docker"){
            // SetUp Nexus Repo to Docker to push image to nexus
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'nexus_creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                             sh '''
                                docker build -t ${params.NexusIp}:${params.NexusRepoPort}/${params.AppName}:${VERSION} .
                                docker login -u $USER -p $PASS ${params.NexusIp}:${params.NexusRepoPort}
                                docker push  ${params.NexusIp}:${params.NexusRepoPort}/${params.AppName}:${VERSION}
                                docker rmi ${params.NexusIp}:${params.NexusRepoPort}/${params.AppName}:${VERSION}
                            '''
                    }
                }
            }
        }
    }
}