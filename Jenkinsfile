pipeline{
    agent { label 'ubuntu'}
    //tool name: '', type: 'maven'
     tools {
        maven 'mavenmayank'
    }
    stages {
    stage('Checkout') {
        steps {
             // One or more steps need to be included within the steps block.
             git branch: 'testpipeline1', url: 'https://github.com/rajyogya015/dummy-java-maven-app.git'
             stash 'sourcecode'
        }
    }
  stage('Build') {
    steps {
        unstash 'sourcecode'
           sh 'mvn package'
      // One or more steps need to be included within the steps block.
    }
  }

  stage('SonarQube') {
    steps {
      // One or more steps need to be included within the steps block.
      echo "Executing Sonarqube stage"
      sleep 10
    }
  }

  stage('Testing') {
    steps {
        // One or more steps need to be included within the steps block.
        echo "Test Execution done"
        sleep 10
      }
  }

}

}
