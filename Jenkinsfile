pipeline{
    agent any
    stages{
        stage("CheckOut Code: Git"){
            steps{
                script{
                    git branch: 'jenkins_cicd', url: 'https://github.com/nirdeshkumar02/Gradle-Application-CICD.git'
                }
            }
        }
        stage("Sonar Quality Check: SonarQube"){
            agent {
                docker{
                    image 'openjdk:11'
                }
            }
            steps{
                script{
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
    }
}