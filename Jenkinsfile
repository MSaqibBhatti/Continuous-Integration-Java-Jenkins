pipeline{
    agent any
    tools{
        maven 'MAVEN3'
        jdk "OracleJDK8"
    }
    environment{
        SNAP_REPO = 'ci-jenkins-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'saqib'
        RELEASE_REPO = 'ci-jenkins-release'
        CENTRAL_REPO = 'ci-jenkins-proxy'
        NEXUSIP = "172.31.24.134"
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'ci-jenkins-group'
        NEXUS_LOGIN = 'nexuslogin'
    }
    stages{
        stage('BUILD'){
            steps{
                sh 'mvn -s settings.xml -DskipTests install'
            }
        }
    }
}