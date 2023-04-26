pipeline{
    agent any

    parameters{
        string(name: 'NexusIp', description: "Provide your public ip of nexus, it will be use for docker", defaultValue: '18.233.162.34')
        string(name: 'NexusPort', description: "Provide your port of nexus", defaultValue: '8081')
        string(name: 'NexusRepoPort', description: "Provide your port of docker-nexus-repo, it will be use for docker", defaultValue: '8083')
        string(name: 'NexusHelmRepoName', description: "Provide name to your helm nexus repo", defaultValue: 'helm-hosted')
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
            // configure gmail to jenkins and then use gmail to send update over email about docker image build and push
            post{
                always{
                    mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "hackingstudio.tcp@gmail.com";
                }
            }
        }
        stage("Helm Charts Misconfig Identification: Datree"){
            steps{
                dir('kubernetes/') {
                    withEnv(['DATREE_TOKEN=GJdx2cP2TCDyUY3EhQKgTc']) {
                            sh 'helm datree test myapp/'
                    }
                }
            }
        }
        stage("Push Helm Charts to Nexus: Nexus"){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'nexus_creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                          dir('kubernetes/') {
                             sh """
                                 helmversion=$( helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
                                 tar -czvf  myapp-${helmversion}.tgz myapp/
                                 curl -u ${USER}:${PASS} http://${params.NexusIp}:${params.NexusPort}/repository/${NexusHelmRepoName}/ --upload-file myapp-${helmversion}.tgz -v
                            """
                          }
                    }
                }
            }
        }
        stage('Send For Approval To Deploy: Email'){
            steps{
                script{
                    timeout(10) {
                        mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> Go to build url and approve the deployment request <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "nksainiji4@gmail.com";  
                        input(id: "Deploy Gate", message: "Deploy ${params.project_name}?", ok: 'Deploy')
                    }
                }
            }
        }
        /*
            Install Kubernetes Continuous Deploy Plugin
            Copy KubeCOnfig file from Kubernetes to Jenkins Machine
            Create Another Credential Choose - Kind as Kubernetes Configuration, Give some Id, Now, For KubeConfig You have 3 option:
                - Enter Directly - Put the data directly to Jenkins
                - From a file on Jenkins Machine - Directly Access  (we choose this) => Provide the file path "/home/ubuntu/kconfig"
                - From a file on Kubernetes Master Node - SSH Required
            Create a secret for image authentication to nexus repo like:
                kubectl create secret docker-registry registry-secret --docker-server=<nexusIP:Port> --docker-username=<nexus-username> --docker-password=<nexus-pass>
        */
        stage('Deploying application on k8s cluster') {
            steps {
               script{
                   withCredentials([kubeconfigFile(credentialsId: 'kubernetes-config', variable: 'KUBECONFIG')]) {
                        dir('kubernetes/') {
                          sh """
                                helm upgrade --install --set image.repository="${params.NexusIp}:${params.NexusRepoPort}/${params.AppName}" --set image.tag="${VERSION}" "${params.AppName}" myapp/ 
                            """ 
                        }
                    }
               }
            }
        }
        stage('verifying app deployment') {
            steps{
                script{
                     withCredentials([kubeconfigFile(credentialsId: 'kubernetes-config', variable: 'KUBECONFIG')]) {
                        sh """
                            kubectl run curl --image=curlimages/curl -i --rm --restart=Never -- curl "${params.AppName}"-myapp:8080
                        """
                     }
                }
            }
        }

    }
    post{
        always{
            mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "hackingstudio.tcp@gmail.com";
        }
    }
}