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
    }

    stages {
        stage('Build'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
        }
    }
}