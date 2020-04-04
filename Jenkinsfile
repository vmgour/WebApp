
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "artifactory"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
    
 rtMaven.tool = "maven"
	
	    stage('Clone sources') {
        git url: 'https://github.com/vmgour/WebApp.git'
    }

	try {
	stage("Building SONAR ...") {
	sh './gradlew clean sonarqube'
		}
		} catch (e) {emailext attachLog: true, body: 'See attached log', subject: 'BUSINESS Build Failure', to: 'vmgour@gmail.com'
	step([$class: 'WsCleanup'])
	return
		}
	
    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
    }

    stage('Maven build') {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
    }

    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
    }
	 
