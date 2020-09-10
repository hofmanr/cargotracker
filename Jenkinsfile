pipeline {
    agent {
        label "master"
    }
    tools {
        maven "Maven"
    }

    stages {
        stage("Clone from VCS") {
            steps {
                script {
                    git branch: "${BRANCH_NAME}", credentialsId: 'github-user-credential', url: 'https://github.com/hofmanr/cargotracker.git'
                    // git 'https://github.com/hofmanr/cargotracker.git';
                }
            }
        }

        stage("Maven Build") {
            steps {
                script {
                    sh 'mvn help:active-profiles'
                    sh "mvn package -DskipTests=true"
                }
            }
        }

        stage("Deploy") {
            steps {
                configFileProvider(
                    [configFile(fileId: 'global-maven-settings', variable: 'MAVEN_SETTINGS')]) {
                        sh 'mvn -s $MAVEN_SETTINGS help:active-profiles'
                        sh 'mvn -s $MAVEN_SETTINGS -DskipTests deploy -Pjenkins'

                    }
            }
        }

        stage('Archive TestResults') {
            junit '**/target/surefire-reports/TEST-*.xml'
            archiveArtifacts 'target/*.war'
        }
    }
}
