node {
    environment { 
        APP_NAME = 'translationApp'
    }

    try{
        def root = tool name: 'Go 1.8', type: 'go'

        notifyBuild('STARTED')

        ws("${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}/") {
            withEnv(["GOROOT=${root}", "PATH+GO=${root}/bin"]) {
                env.PATH="${GOPATH}/bin:$PATH"
                
                stage('Checkout'){
                    echo 'Checking out SCM'
                    checkout scm
                }
                
                stage('Prepare'){
                    echo 'Pulling Dependencies'
            
                    sh 'go version'
                    sh 'go get -u google.golang.org/api/gmail/v1'
                    sh 'go get -u golang.org/x/oauth2/google'
                    
                    //or -update
                    sh 'cd ${GOPATH}/src/cmd/EmailTranslationSystem/' 
                }
            
                stage('Build'){
                    echo 'Building Executable'
                
                    //Produced binary is $GOPATH/src/cmd/EmailTranslationSystem
                    sh """cd $GOPATH/src/cmd/EmailTranslationSystem/ && go build -o ${APP_NAME}"""
                }

                stage('Execute'){
                    echo 'Executing binary'

                    sh """. ./${APP_NAME}"""
                }
            }
        }
    }catch (e) {
        // If there was an exception thrown, the build failed
        currentBuild.result = "FAILED"
    } finally {
        // Success or failure, always send notifications
        notifyBuild(currentBuild.result)
    }
}

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} <${env.BUILD_URL}|Job URL> - <${env.BUILD_URL}/console|Console Output>"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }
}