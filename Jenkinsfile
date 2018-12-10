
//Author: Mario E. Montenegro

def dirsChanged = []

def performIntegrationStages(String app) {
    dir ("${app}") {
        pom = readMavenPom file: 'pom.xml'
        pom.version
        pom.groupid
        stage("test") {
            echo "Testing the app [${app}]"
        }
        stage("build") {
            echo "Building the app [${app}]"
        }
    }
}

pipeline {
    agent {
        label 'master'
    }

    stages {

        stage('Discover Apps') {
            steps {
                script {
                    // This command results in output indicating several one of these and the affected files:
                    // Added (A), Copied (C), Deleted (D), Modified (M), Renamed (R)
                    def commitChangeset = sh(returnStdout: true, script: 'git diff-tree --no-commit-id --name-only -r HEAD').trim()
                    for (dirChanged in commitChangeset) {
                        if (dirChanged != 'Jenkinsfile') {
                            dirsChanged += dirChanged
                        }
                    }
                }
            }
        }

        stage('parallel stage') {
            steps {
                script {
                    def apps = [:]
                    for (app in dirsChanged) {
                        performIntegrationStages(app)
                    }
                    parallel apps
                }
            }
        }
    }
}