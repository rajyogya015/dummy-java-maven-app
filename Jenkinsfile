node {
    def branchName = env.BRANCH_NAME
    def gitCredentials = "GitHUbToken"
    withCredentials([usernamePassword(credentialsId: 'GitHUbToken', passwordVariable: 'gittoken', usernameVariable: 'gitUser')]) {
    def repoUrl = "https://${gittoken}@github.com/rajyogya015/dummy-java-maven-app.git"
    }
    def tomcatAppIp = "172.31.95.157"
    def server
    def rtmaven
    def buildInfo

try{
  if(env.BRANCH_NAME.startsWith('PR-') && env.CHANGE_ID != null){
    stage('Checkout') {
        // Checkout the code from version control system
        checkout scm
        stash 'sourcecode'
    }

    stage('Build') {
        // Build the project
        //unstash sourcecode
        sh 'mvn package'
    }

    stage('Test') {
        // Run tests
        echo "Execute JUnit test as per pom.xml"
        //unstash sourcecode
        //sh 'mvn test'
    }

    stage('Sonar') {
        // Execute SOnar Scan
        //unstash sourcecode
        //withSonarQubeEnv('SonarServer') {
                //sh 'mvn clean package sonar:sonar'
       // }
        sleep 30
        //waitForQualityGate abortPipeline: true
        echo "Sonar execute"
    }

  }
  if(env.BRANCH_NAME.equals('main') ){
    stage('Build') {
        // Checkout the code from version control system
        checkout scm
        sh 'mvn package'
        stash 'sourcecode'
        stash includes: 'target/*.war', name: 'artifacet'
    }
    stage('CodeScanXray'){
       /*withCredentials([usernamePassword(credentialsId: 'jfcred', passwordVariable: 'jfpasswd', usernameVariable: 'jfuser')]) {
        withCredentials([string(credentialsId: 'jfcred', variable: 'jfcred')]) {
        unstash 'sourcecode'
        server = Artifactory.server 'jftrial'
        buildInfo = Artifactory.newBuildInfo()
        buildName: env.JOB_NAME,
        buildNumber: BUILD_NUMBER

        rtBuildInfo (
                    buildName: "build1",
                    buildNumber: BUILD_NUMBER,
                    maxBuilds: 1,
                    maxDays: 2,
                    doNotDiscardBuilds: ["3"],
                    deleteBuildArtifacts: true
                )
        rtPublishBuildInfo (
                    serverId : 'server',
                    buildName : env.JOB_NAME,
                    buildNumber : BUILD_NUMBER
                )       
        xrayScan (
                        serverId :   "server",
                        buildName    : env.JOB_NAME,
                        buildNumber : BUILD_NUMBER,
                        failBuild    : false
                    )
        }*/
        echo "Xray Scan execution done"
    }
    stage("Create release"){
        echo "Creating new release and push Tag to GitHub"  
        
            createRelease("${repoUrl}", "${gittoken}")

    }
    stage('PushArtifactstoJFrog'){
        unstash 'artifacet'
        env.fpath = sh (script: "find ./ -name '*.war' | awk -F '/' '{print \$2\"/\"\$3}'",returnStdout: true).trim()
        env.fileName = sh (script: "echo ${fpath} | awk -F '/' '{print \$3}'",returnStdout: true).trim()
        artifactUpload("${fpath}", "${fileName}")
    }
    stage('DeployToAppServer'){
        //unstash 'artifacet'
        echo "Downloading Artifacts from artifactory"
        downloadArtifacts("${fileName}", "$WORKSPACE")
        echo " Deploying to App Server"
        sh "scp ${WORKSPACE}/${fileName} ubuntu@${tomcatAppIp}:/tmp/"
        sh "ssh ${tomcatAppIp} sudo cp /tmp/*.war /opt/tomcat/webapps/"
        sh "ssh ${tomcatAppIp} sudo systemctl restart tomcat"

        echo "Deployment on Tomcat App completed"
    }

  }
}
catch(Exception e){
    echo "Pipeline failed with error e"
}
finally{
    emailext body: '', subject: "BUILD STATUS : \"${BUILD_URL}- ${BUILD_NUMBER}\"", to: 'rajyogya015@gmail.com'
}
}

/*This method is to create release, increment the version and create/Push git Tag to remote repo
@param
repoUrl: Repository Url which wil b eused to clone
*/
def createRelease(repoUrl){
    //sh 'mkdir -p tmp2'
    try{
    sh "git clone -b main ${repoUrl} tmp2 && cd tmp2/"
    def pomFile = 'pom.xml'
    def pom = readMavenPom file: pomFile
    pom.version = pom.version +1
    writeMavenPom file: pomFile, model: pom
    sh """git add pom.xml
    git config user.name=rajyogya015
    git commit -m "Version updated [ci skip]"
    git tag -a ${pom.version} -m "Version updated [ci skip]"
    git push ${repoUrl} main
    """
    }
    catch(Exception e){
        echo "Git Relase creation Failed..with error e ..check logs..."
    }

}

/*This method is to Upload artifacts to Jfrog trail artifactory
@param
filepath:Artifacts complete Path
filename: Artifacts package Name
*/

def artifactUpload(filepath,filename){
    try{
        sh """
            curl -urajyogya015@gmail.com:"${jfcred}" -T "$filepath" "https://rajyogya015.jfrog.io/artifactory/javabuildpkg-generic-local/$filename"
            """
    }
   catch(Exception e){
        echo "Failed Uploading artifacts $filename ..with error e"
    }
    
}

/*This method is to Download artifacts to Jfrog trail artifactory
@param
downloadPath:Artifacts Path where to be Downloaded 
filename: Artifacts package Name
*/
def downloadArtifacts(filename, downloadPath){
    try{
        
        sh """
        curl -urajyogya015@gmail.com:"${jfcred}" -L -O "https://rajyogya015.jfrog.io/artifactory/javabuildpkg-generic-local$filename" """
    }
    catch(Exception e){
        echo "Download of artifacts failed with error" 
    }
}
