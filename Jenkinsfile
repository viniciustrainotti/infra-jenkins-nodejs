pipeline {
    agent any
    parameters { choice(name: 'CHOICES', choices: ['serve', 'serverx'], description: 'Infra Jenkins Build Nodejs') }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "172.17.0.3:8081"
        NEXUS_REPOSITORY = "jenkins-repo"
        NEXUS_CREDENTIAL_ID = "nexus"
    }
    //def dist_project = "dist-frontend-ead-grade-dashboard.zip"
    stages {
        stage('Clone and Checkout') {
            steps {
                git credentialsId: 'git_id',
                url: 'http://github.com/viniciustrainotti/service-ead-grade-dashboard.git',
                branch: 'master'

                sh "git checkout master"
                sh "pwd"
                sh "ls -lat"
            }
        }
        stage('Build'){
            steps{
                script {
                    if("$CHOICES".indexOf('serve') != -1){
                        sh 'echo BUILD_PROD'
                        sh 'npm install'
                        //sh 'npm run serve'
                    }else{
                        sh 'echo BUILD_DEV'
                        sh 'npm install'
                        //sh 'npm run servex'
                    }
                }
            }
        }
        stage('Zip'){
            steps {
                script {
                    sh 'echo ZIP dist'
                    sh 'zip dist.zip *'
                }
            }
        }
        stage('Publish to Nexus Repository Manager'){
            steps{
                script{
                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: "${env.JOB_BASE_NAME}",
                                classifier: '',
                                file: "dist.zip",
                                type: "zip"]
                            ]
                    )
                }
            }
        }
    }
}
