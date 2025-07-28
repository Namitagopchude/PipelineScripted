node ('NamitaNode') {
    // Environment variable
    env.JIRA_SITE = 'Namita_jira_sites'  // Make sure this matches the "Jira Site" configured in Jenkins

    try {
        stage('checkout code') {
            checkout scm
        }

        stage('build') {
            bat 'mvn clean install -Denforcer.skip=true'
        }

        stage('SonarQube Analysis') {
            withCredentials([string(credentialsId: 'Namita_Sonar', variable: 'Namita_Sonar')]) {
                bat "mvn sonar:sonar -Dsonar.projectKey=Jenkins-maven -Dsonar.projectName=\"Jenkins-maven\" -Dsonar.token=${Namita_Sonar}"
            }
        }

        stage('Archive HTML Reports') {
            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'target/site/jacoco',
                reportFiles: 'index.html',
                reportName: 'Code Coverage'
            ])
        }

        stage('Archive Artifacts') {
            archiveArtifacts artifacts: '**/target/*.jar'
        }

        stage('deploy') {
            bat 'java -jar "%WORKSPACE%/target/my-app-1.0-SNAPSHOT.jar"'
        }

        stage('Update Jira') {
            jiraAddComment(
                idOrKey: 'JEN-1',
                comment: '✅ Build completed successfully.',
                site: env.JIRA_SITE,
                failOnError: false
            )
        }
        
    } 

}
