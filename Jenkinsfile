pipeline {
  agent any
  
  environment {
    PATH = "/opt/maven/bin:$PATH"
  }
  
  stages {

    stage('Cloning Project') {
      steps {
        git(url: 'https://github.com/bibah94/BiB-Devops.git', branch: 'master')
      }
    }

    stage('Build app'){
      steps {
        sh "mvn clean install package"
      }
    }

    stage('Build Docker Image, Push to Nexus and Deploy to Docker Container stage'){
      steps {
        sshPublisher(publishers: [sshPublisherDesc(
          configName: 'ansible-server', 
          transfers: [sshTransfer(
            cleanRemote: false, excludes: '', 
            execCommand: 'ansible-playbook -i /opt/docker/hosts  /opt/docker/devops-docker-image.yml;', 
            execTimeout: 120000, 
            flatten: false, 
            makeEmptyDirs: false, 
            noDefaultExcludes: false, 
            patternSeparator: '[, ]+', 
            remoteDirectory: '//opt//docker', 
            remoteDirectorySDF: false, 
            removePrefix: 'webapp/target', 
            sourceFiles: 'webapp/target/*.war')], 
            usePromotionTimestamp: false, 
            useWorkspaceInPromotion: false, 
            verbose: false
        )])
      }
    }
    
  }
  post{
		success{
			slacksend	channel: '#jenkins',
						color: 'good',
						message: "Build ${env.BUILD_NUMBER}, success: ${currentBuild.fullDisplayName}."
		}
		failure{
			slacksend	channel: '#jenkins',
						color: 'danger',
						message: "Build ${env.BUILD_NUMBER}, failed: ${currentBuild.fullDisplayName}."	
		}
    always {
      emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
           recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
           to: 'habibndiaye08@gmail.com',
           subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
      }
	}
}
