pipeline {
    agent any
    tools {
        maven 'maven'
        }
    environment {
        git_url  = "https://github.com/jaiswaladi246/Petclinic.git"
        branch_name = 'main'
        SONAR_PROJECT_KEY = 'Qualty' 
        channel_name = 'test-case'
        email_recipients = "aayush.verma@mygurukulam.co"
      
    }
    stages {
        stage("git clone"){
          steps {
            git url: git_url, branch: branch_name
            }
        }
        stage('Quality Gate analysis'){
            steps{
                withSonarQubeEnv('sonar') {
                withCredentials([string(credentialsId: 'sonarTocken', variable: 'SONARQUBE_TOKEN')]) {
                sh '''
                mvn clean verify sonar:sonar \
                    -Dsonar.projectKey="${SONAR_PROJECT_KEY}" \
                    -Dsonar.token="${SONARQUBE_TOKEN}" \
                    -Dsonar.qualitygate.wait=true \
                    -Dsonar.projectName='Qualty' 
                '''
                }
                }
            }
        }
        stage("Quality Gate pass"){
          steps {
            echo 'Quality Gate pass'
            }
        }
    }
    post{
        success {
            slackSend(channel: "${channel_name}", color: 'good', username: 'Jenkins CI',  message: "Successful: Quality Gate passed and report published on SonarQube Server. Job Details - Name: ${env.JOB_NAME}, Build Number: ${env.BUILD_NUMBER}, URL: ${env.BUILD_URL}")
            emailext(
                body: """
                    Hello,
    
                    The Jenkins pipeline **${env.JOB_NAME}** has completed successfully on **Build #${env.BUILD_NUMBER}**.
    
                    **Build Details:**
                    - **Job Name:** ${env.JOB_NAME}
                    - **Build Number:** ${env.BUILD_NUMBER}
                    - **Build URL:** ${env.BUILD_URL}
    
                  Quality Gate report published on SonarQube Server project name:- ${env.projectKey_name}
    
                    Best regards,  
                    Jenkins CI
                    Zero Downtime Crew
                """,
                subject: "Jenkins Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                to: email_recipients
            )
        }
        failure {
            slackSend(channel: "${channel_name}", color: 'danger', username: 'Jenkins CI', message: "FAILURE: Quality Gate Failed. Check logs & report on SonarQube Server for more details. Job Details - Name: ${env.JOB_NAME}, Build Number: ${env.BUILD_NUMBER}, URL: ${env.BUILD_URL}")

            emailext(
                body: """
                    Hello,
    
                    The Jenkins pipeline **${env.JOB_NAME}** has failed on **Build #${env.BUILD_NUMBER}**.
    
                    **Job Details:**
                    - **Job Name:** ${env.JOB_NAME}
                    - **Build Number:** ${env.BUILD_NUMBER}
                    - **Build URL:** ${env.BUILD_URL}
    
                    Please review logs & report on SonarQube Server for more details.
    
                    Regards,  
                    Jenkins CI
                    Zero Downtime Crew
                """,
                subject: "Jenkins Pipeline FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                to: email_recipients
            )
        }
    }
}
