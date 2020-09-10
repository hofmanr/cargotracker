pipeline {
    agent {
        label "master"
    }
    tools {
        maven "Maven"
    }

//     stages {
//         stage("Clone from VCS") {
//             steps {
//                 script {
//                     git branch: "${BRANCH_NAME}", credentialsId: 'github-user-credential', url: 'https://github.com/hofmanr/cargotracker.git'
//                     // git 'https://github.com/hofmanr/cargotracker.git';
//                 }
//             }
//         }

        stage("Build") {
            when { anyOf { branch 'release/*'; branch 'master'; branch 'feature/*'; branch 'hotfix/*' } }
            steps {
                script {
                    sh 'mvn help:active-profiles'
                    sh "mvn clean package"
                }
            }
        }

        stage("Deploy") {
            when { anyOf { branch 'master'; branch 'feature/*'; branch 'hotfix/*' } }
            steps {
                configFileProvider(
                    [configFile(fileId: 'global-maven-settings', variable: 'MAVEN_SETTINGS')]) {
                        sh 'mvn -s $MAVEN_SETTINGS help:active-profiles'
                        sh 'mvn -s $MAVEN_SETTINGS -DskipTests deploy -Pjenkins'
                    }
            }
        }

       stage("Archive") {
            when { anyOf { branch 'feature/*' } }
            steps {
                archiveArtifacts artifacts: 'target/*.war', followSymlinks: false
            }
        }
    }

}
