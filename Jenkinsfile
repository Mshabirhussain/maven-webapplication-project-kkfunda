node {

    def mvnHome = tool 'maven-3.9.10'

    stage('Checkout') {
        git branch: 'master',
            url: 'https://github.com/Mshabirhussain/maven-webapplication-project-kkfunda.git'
    }

    stage('Compile') {
        sh "${mvnHome}/bin/mvn clean compile"
    }

    stage('SonarQube Analysis') {
        sh "${mvnHome}/bin/mvn sonar:sonar"
    }
    stage('SQ Approval') {
        def decision = input(
            id: 'SQApproval',
            message: 'Please review the SonarQube report and approve/reject the pipeline.',
            parameters: [
                choice(
                    name: 'ACTION',
                    choices: ['Approve', 'Reject'],
                    description: 'Select your action'
                )
            ]
        )

        if (decision == 'Reject') {
            error("Pipeline rejected after SonarQube review.")
        }

        echo "Pipeline approved. Proceeding to next stage..."
    }
    stage('Package') {
        sh "${mvnHome}/bin/mvn package"
    }

    stage('Deploy to Nexus') {
        sh "${mvnHome}/bin/mvn deploy"
    }
    stage('Deploy to Tomcat') 
    {
      
      sh """

      curl -u admin:admin \
--upload-file /var/lib/jenkins/workspace/pipeline-pratice/target/maven-web-application.war \
"http://localhost:8085/manager/text/deploy?path=/maven-web-application&update=true"
          
        """
    }
}