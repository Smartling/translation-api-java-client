#!groovy

pipeline {
    agent any

    options {
        buildDiscarder(logRotator(artifactDaysToKeepStr: '7', artifactNumToKeepStr: '10', daysToKeepStr: '7', numToKeepStr: '10'))
    }

    stages {
        stage('Run tests & Junit report') {
            agent {
                label 'master'
            }

            steps {
                sh '/opt/apache-maven-3.3.9/bin/mvn clean test'
                stash 'test-results'
            }
            post {
                always {
                    junit "**/surefire-reports/*.xml"
                }
            }
        }

//        stage('Sonar: scan') {
//            agent {
//                label 'master'
//            }
//
//            steps {
//                // Discard older builds
//                milestone ordinal: 1, label: 'Sonar'
//
//                unstash 'test-results'
//
//                withSonarQubeEnv('sonar') {
//                    sh '/opt/apache-maven-3.3.9/bin/mvn sonar:sonar -Dsonar.projectVersion=${BUILD_NUMBER}'
//                }
//            }
//        }
//
//        stage("Sonar: quality gate") {
//            steps {
//                script {
//                    try {
//                        timeout(time: 5, unit: 'MINUTES') {
//                            def qg = waitForQualityGate()
//                            if (qg.status != 'OK') {
//                                error "Pipeline aborted due to quality gate failure"
//                            }
//                        }
//                    }
//                    catch (err) {
//                        // Catch timeout exception but not Quality Gate.
//                        String errorString = err.getMessage();
//
//                        if (errorString == "Pipeline aborted due to quality gate failure") {
//                            error errorString
//                        }
//                    }
//                }
//            }
//        }

        stage('Build and publish jar') {
            agent {
                label 'master'
            }

            steps {
                configFileProvider(
                    [configFile(
                        fileId: 'global-maven-setings',
                        targetLocation: 'settings.xml',
                        variable: 'mavenSettingsPath',
                        replaceTokens: true
                    )]
                ) {
                    sh '/opt/apache-maven-3.3.9/bin/mvn clean deploy -s ${mavenSettingsPath} -P publish -DskipTests'
                }
            }
        }
    }

    post {
        always {
          deleteDir()
        }
    }
}
