pipeline {
    agent { label 'JDK8_Maven' }
    parameters { 
        string(name: 'Maven_Goal', defaultValue: 'package')
    }
    triggers { pollSCM('H/5 * * * *') }
    stages {
        stage('vcs') {
            steps {
                git url: 'https://github.com/dumyrepositories/game-of-life.git',
                    branch: 'declarative'
            }
        }
        stage('build') {
            tools {
                jdk 'Ubuntu_JDK8'
            }
            steps {
                sh "mvn ${params.Maven_Goal}"
            }
        }
        stage('post-build') {
            steps {
                archiveArtifacts artifacts: '**/target/*.war',
                                 onlyIfSuccessful: true
                junit testResults: '**/surefire-reports/TEST-*.xml'
            }
        }
        stage('copy-file') {
            steps {
                sh "mkdir -p /tmp/${JOB_NAME}/${BUILD_ID} && cp **/target/*.war /tmp/${JOB_NAME}/${BUILD_ID}"
            }
        }
    }
    post {
        success {
            mail subject: "The ${JOB_NAME} buld id ${BUILD_ID} is success",
                 body: "Check more information about build using URL ${BUILD_URL}",
                 to: 'team-all@devops.in',
                 from: 'jenkins@jobs.info'
        }
        failure {
           mail subject: "The ${JOB_NAME} buld id ${BUILD_ID} is failure",
                 body: "Check more information about build using URL ${BUILD_URL}",
                 to: 'team-all@devops.in, test@checkteam.devops',
                 from: 'jenkins@jobs.info' 
        }
    }
}