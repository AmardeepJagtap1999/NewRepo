pipeline {

    agent any

    triggers {
        cron('H */8 * * *') //regular builds
        pollSCM('* * * * *') //polling for changes, here once a minute
    }
    
    stages {
        stage('Checkout') {
            steps { //Checking out the repo
                checkout changelog: true, poll: true, scm: [$class: 'GitSCM', branches: [[name: '*/master']], browser: [$class: 'BitbucketWeb', repoUrl: 'https://web.com/blah'], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/AmardeepJagtap1999/NewRepo.git']]]
            }
        }
    stage('Unit & Integration Tests') {
                        steps {
                                script {
                                        try {
                                                sh './gradlew clean test --no-daemon' //run a gradle task
                                        } finally {
                                                junit '**/build/test-results/test/*.xml' //make the junit test results available in any case (success & failure)
                                        }
                                }
                        }
                }
                stage('Frontend Unit Tests') {
                        steps {
                                sshagent(['git']) {
                                        script {
                                                try {
                                                        sh './gradlew cleanFrontendTest --no-daemon'
                                                        sh './gradlew frontendUnitTest --no-daemon'
                                                } finally {
                                                        junit 'publicapi/frontend/test/karma-result.xml'
                                                }
                                        }
                                }
                        }
                }
                stage('Frontend Static Code Analysis') {
                        steps {
                                script {
                                        try {
                                                sh './gradlew tslint --no-daemon'
                                        } finally { //Make checkstyle results available
                                                checkstyle canComputeNew: false, defaultEncoding: '', healthy: '', pattern: 'publicapi/frontend/tslint-result.xml', unHealthy: ''
                                        }
				}
                        }
                }
        }
}
