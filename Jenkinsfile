pipeline {
    agent any
    tools {
        maven "maven3.9"
        jdk "JDK17"
    }
    
    environment {
        SNAP_REPO = 'vpro-snap'
		NEXUS_USER = 'admin'
		NEXUS_PASS = 'admin'
		RELEASE_REPO = 'vprof'
		CENTRAL_REPO = 'vpro-central'
		NEXUSIP = '172.31.93.153'
		NEXUSPORT = '8081'
		NEXUS_GRP_REPO = 'vpro-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARSCANNER = 'sonarscanner'
        SONARSERVER = 'sonarserver'
        SLACK_CHANNEL = '#vpro-cicd'
    }

    stages {
        stage('Build'){
            steps {
                sh 'mvn -DskipTests install'
            }
            post {
                success {
                    echo 'Build was successful'
                    archiveArtifacts artifacts:'**/*.war'
                }
                
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('checkstyle analysis') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage ('SonarQube analysis') {
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps {
                withSonarQubeEnv("${SONARSERVER}") {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }
        }
        post {
        success {
            slackSend(
                channel: "${env.SLACK_CHANNEL}",
                color: 'good',
                message: "*SUCCESS:* Job `${env.JOB_NAME}` #${env.BUILD_NUMBER} completed successfully. :tada:\n${env.BUILD_URL}"
            )
        }

        failure {
            slackSend(
                channel: "${env.SLACK_CHANNEL}",
                color: 'danger',
                message: "*FAILURE:* Job `${env.JOB_NAME}` #${env.BUILD_NUMBER} failed. :x:\nCheck the logs: ${env.BUILD_URL}"
            )
        }

        unstable {
            slackSend(
                channel: "${env.SLACK_CHANNEL}",
                color: 'warning',
                message: "*UNSTABLE:* Job `${env.JOB_NAME}` #${env.BUILD_NUMBER} is unstable. :warning:\nDetails: ${env.BUILD_URL}"
            )
        }

        always {
            slackSend(
                channel: "${env.SLACK_CHANNEL}",
                color: '#439FE0',
                message: "*BUILD COMPLETED:* `${env.JOB_NAME}` #${env.BUILD_NUMBER}\nStatus: *${currentBuild.currentResult}*\n<${env.BUILD_URL}|View Build>"
            )
        }
    }
 }

