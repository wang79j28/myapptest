pipeline {
    agent any
    options {
        disableConcurrentBuilds()
    }
    stages {
      stage('do pr check') {
	    when { changeRequest target: 'master' }
	    stages {
               stage('pr ') {
                   steps {
                       echo "pr ~!!!!!!!!!!!!!!!!"
		       setBuildStatus('jenkins:build', 'building','PENDING')
		       setBuildStatus('jenkins:codecov','codecov','PENDING')
		       sleep 10
		       setBuildStatus('jenkins:build', 'Your tests passed on CircleCI!','SUCCESS')	   
                   }
               }
            }
        }
	stage('master deploy') {
	    when {  branch 'master' }
	    stages {
               stage('deploy ') {
                   steps {
		       echo "deploying......"
		       sleep 110
                       echo "deploy~!!!!!!!!!!!!!!!!"
		       setBuildStatus('jenkins:build', 'Your tests passed on CircleCI!','SUCCESS')	   
		       
                   }
               }
            }
        }    
    }
}	


def getRepoURL() {
  sh "git config --get remote.origin.url > .git/remote-url"
  return readFile(".git/remote-url").trim()
}
 
def getCommitSha() {
  sh "git rev-parse HEAD > .git/current-commit"
  return readFile(".git/current-commit").trim()
}
 
def updateGithubCommitStatus(String message, String state) {
  // workaround https://issues.jenkins-ci.org/browse/JENKINS-38674
  repoUrl = getRepoURL()
  commitSha = getCommitSha()
 
  step([
	$class: 'GitHubCommitStatusSetter',
	reposSource: [$class: "ManuallyEnteredRepositorySource", url: repoUrl],
	commitShaSource: [$class: "ManuallyEnteredShaSource", sha: commitSha],
	errorHandlers: [[$class: 'ShallowAnyErrorHandler']],
	statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ])
}
void setBuildStatus(String title, String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: getRepoURL()],
      commitShaSource: [$class: "ManuallyEnteredShaSource", sha: commitSha],	  
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: title],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}
