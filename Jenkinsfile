node {
  if(env.BRANCH_NAME.startsWith('PR-') && env.CHANGE_ID != null){
    stage('Checkout') {
        // Checkout the code from version control system
        checkout scm
    }

    stage('Build') {
        // Build the project
        sh 'mvn clean install'
    }

    stage('Test') {
        // Run tests
        echo "Run Unit Test cases"
    }

    stage('Sonar') {
        // Publish artifacts
        echo "Sonar execute"
    }

    post {
        // Post-build actions
        success {
            // Perform some action on success
            echo 'Build successful'
        }

        failure {
            // Perform some action on failure
            echo 'Build failed'
        }
    }
  }
}
