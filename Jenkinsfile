pipeline {
  agent none
  environment {
    PATH = "/opt/maven/bin:$PATH"
    scannerHome = tool 'sonar-scanner'
  }
  triggers {
    pollSCM '* * * * *'
  }

  stages {

    stage('Cloning Project') {
      steps {
        //enable remote triggers
        script {
            properties([pipelineTriggers([pollSCM('')])])
        }
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
}