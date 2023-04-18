node{
  stage('SCM Checkout'){
    git 'https://github.com/rajyogya015/dummy-java-maven-app.git'
  }
  stage('Compile package'){
    //Defining Maven Home path
    def MVN_HOME = tool name: 'maven-3', type: 'maven'
    sh "${MVN_HOME}/bin/mvn clean package"
    
  stage('SonarQube analysis'){
    def MVN_HOME = tool name: 'maven-3', type: 'maven'
    withSonarQubeEnv('sonar-1') {
    sh "${MVN_HOME}/bin/mvn sonar:sonar"
    }
  }  
  }
  stage('Email Notification'){
    mail bcc: '', body: '''Hello,
    This email is to inform you that the build job has been triggered.

    Thanks and Regards,
    Yogya''', cc: '', from: '', replyTo: '', subject: 'Jenkins Build Info', to: 'rajyogya015@gmail.com'

  }

}
