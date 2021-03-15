pipeline {
    agent any
    parameters { choice(name: 'CHOICES', choices: ['build_prod', 'build_dev'], description: ' Teste Checkout Git into Jenkins') }
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
                url: 'http://git.franciscanos.net/moodle/frontend-ead-grade-dashboard.git',
                branch: 'master'

                sh "git checkout master"
                sh "pwd"
                sh "ls -lat"
            }
        }
        stage('Build'){
            steps{
                script {
                    if("$CHOICES".indexOf('build_prod') != -1){
                        sh 'echo BUILD_PROD'
                        sh 'npm install'
                        sh 'npm run build_prod'
                    }else{
                        sh 'echo BUILD_DEV'
                        sh 'npm install'
                        sh 'npm run build_dev'
                    }
                }
            }
        }
        stage('Zip'){
            steps {
                script {
                    sh 'echo ZIP dist'
                    sh 'zip dist.zip dist/*'
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