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
                scannerhome = tool "$(SONARSCANNER)"
            }
            {
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
 }
}