pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        cleanRemote: false,
                                        //excludes: '’,
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule',
                                        execTimeout: 120000,
                                        flatten: false,
                                        noDefaultExcludes: false, 
				        //patternSeparator: '[, ]+’,
                                        remoteDirectory: '/tmp',
                                        remoteDirectorySDF: false,
                                        removePrefix: 'dist/',
                                        sourceFiles: 'dist/trainSchedule.zip',
                                        usePromotionTimestamp: false, 
				        useWorkspaceInPromotion: false,
                                        verbose: true
                                        
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
