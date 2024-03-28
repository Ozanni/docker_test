void setBuildStatus(String message, String state) {
    step([
        $class: "GitHubCommitStatusSetter",
        reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/Ozanni/docker_test.git"],
        contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
        errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
        statusResultSource: [$class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]]]
    ]);
}


pipeline {
    agent any
    stages {
        // stage('pull code'){
        //     steps{
        //         checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Ozanni/ris_portal.git']]])
        //     }
        // }
        stage('Build') {
            steps {
                script {
                    setBuildStatus("Build in progress", "PENDING")
                    sh '''
                        docker build -t test2 .
                        docker tag test2 vannguyen02/test2:latest
                    '''
                    
                    
                }
            }
        }
        stage('push docker image') {
            steps {
                withCredentials([string(credentialsId: 'Docker-hub-password', variable: 'PASSWORD')]) {
                        sh '''
                            docker login -u vannguyen02 -p $PASSWORD
                            docker push vannguyen02/test2
                        '''
                    }
            }
        }

        // stage('SCP copy') {
        //     steps {
        //         withCredentials([sshUserPrivateKey(credentialsId: 'ssh-private-key', keyFileVariable: 'MY_SSH_KEY')]) {
        //             sh '''
        //             scp -i $MY_SSH_KEY your-file.txt username@your-remote-server:/path/to/directory/
        //             '''
        //         }
        //     }
        // }
    }
    post {
        success {
            script {
                setBuildStatus("Build succeeded", "SUCCESS");
            }
        }
        failure {
            script {
                setBuildStatus("Build failed", "FAILURE");
            }
        }
    }
}
