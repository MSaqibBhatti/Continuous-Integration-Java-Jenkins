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
        NEXUSIP = "172.31.26.159"
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'ci-jenkins-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARSERVER = 'sonarserver'
        SONARSCANNER = 'sonarscanner'
    }
    stages{
        stage('BUILD'){
            steps{
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post{
                success{
                    echo "Now Archiving"
                    archiveArtifacts artifacts: '**/*.war'
                }
            }    
        }
        stage('Test'){
            steps{
                sh 'mvn -s settings.xml test'
            }
        }
        stage('Checkstyle Analysis'){
            steps{
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }
        stage('Sonar Analysis'){
            environment{
                scannerHome = tool "${SONARSCANNER}"
            }
            steps{
                withSonarQubeEnv("${SONARSERVER}"){
                    sh "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=ci-jenkins \
                        -Dsonar.projectName=ci-jenkins-project \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                        -Dsonar.junit.reportsPath=target/surefire-reports/ \
                        -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml"
                }
            }
        }
        stage('Quality Gate'){
            steps{
                timeout(time:1, unit:'HOURS'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Nexus Artifact Uploader'){
            steps{
                nexusArtifactUploader{
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: "${RELEASE_REPO}",
                    credentialsId: "${NEXUS_LOGIN}",
                    artifacts: [
                        [artifactId: 'java',
                        classifier: '',
                        file: 'target/vprofile-v2.war',
                        type:'war']
                    ]
                }
            }
        }
    }
}