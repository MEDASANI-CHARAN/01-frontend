pipeline {
    agent {
        label 'agent-1'
    }
    options {
                // timeout(time: 100, unit: 'SECONDS')
                timeout(time: 15, unit: 'MINUTES')
                disableConcurrentBuilds()
                // ansiColor('xterm')
            }
    environment {
        def appVersion = ''
        nexusUrl = 'jenkins-nexus.daws2025.online:8081'
    }
    stages {
        stage('read the version'){
            steps {
                script {
                def packageJson = readJSON file: 'package.json'
                appVersion = packageJson.version
                echo "application version: $appVersion"
             }
            }
        }  
        stage('Build') {
            steps {
                sh """
                    zip -q -r frontend-${appVersion}.zip * -x Jenkinsfile -x frontend-${appVersion}.zip
                    ls -ltr
                """
            }
        }

        stage('Nexus Artifact Upload') {
            steps {
               script {
                    nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${nexusUrl}",
                    groupId: 'com.expense',
                    version: "${appVersion}",
                    repository: 'frontend',
                    credentialsId: 'nexus-auth',
                    artifacts: [
                        [artifactId: 'frontend',
                        classifier: '',
                        file: 'frontend-' + "${appVersion}" + '.zip',
                        type: 'zip']
                    ]
                )
             }
          }
        } 
        // stage('Deploy'){
        //     steps {
        //             script {
        //                 def params = [
        //               string(name: 'appVersion', value: "${appVersion}")
        //                 ]
        //                  build job: 'frontend-deployment', parameters: params, wait: false
        //             }
        //         }
        //     }
         }
    post { 
            always { 
                echo 'I will always say Hello again!'
                deleteDir()
            }
            success { 
                echo 'I will run when pipeline sucess'
            }
            failure { 
                echo 'I will run when pipeline failure'
            }
        } 
} 
