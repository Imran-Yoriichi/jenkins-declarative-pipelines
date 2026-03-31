pipeline {
    agent any

    options {
    timestamps()                        // add timestamps to logs
    timeout(time: 10, unit: 'MINUTES') // fail if pipeline takes over 10 mins
    retry(3)                            // retry entire pipeline up to 3 times
    disableConcurrentBuilds()           // only one build at a time
    buildDiscarder(
        logRotator(numToKeepStr: '10')  // keep only last 10 builds
    )
}
    
    environment {
        APP_NAME = 'jenkins-practice-app'
    }

    parameters {
        choice(
            name: 'DEPLOY_ENV',
            choices: ['dev', 'staging', 'production'],
            description: 'Where to deploy?'
        )
        booleanParam(
            name: 'RUN_TESTS',
            defaultValue: true,
            description: 'Run the test suite?'
        )
       string(
    name: 'DEPLOYER_NAME',
    defaultValue: 'ski',
    description: 'Who is deploying?'
)
    }

    stages {
        stage('Cleanup') {
            steps {
                cleanWs()    // wipe the workspace before starting
            }
        }

        stage('Checkout') {
            steps {
                checkout scm    // fresh clone from Git
            }
        }

        stage('Build') {
            steps {
                echo 'Building on a clean workspace!'
            }
        }

        stage('Build') {
            steps {
                echo "Building ${env.APP_NAME}"
                echo "Build number: ${env.BUILD_NUMBER}"
                 echo "JOB NAME : ${env.JOB_NAME}"
                echo "Workspace:   ${env.WORKSPACE}"
                echo "Deployed by: ${params.DEPLOYER_NAME}"
                echo "Jenkins URL: ${env.JENKINS_URL}"
            }
        }

        stage('Test') {
            when {
                expression { params.RUN_TESTS == true }
            }
            steps {
                echo 'Running test suite...'
                echo 'All tests passed!'
            }
        }

        stage('Deploy to Dev') {
            when {
                expression { params.DEPLOY_ENV == 'dev' }
            }
            steps {
                echo 'Deploying to DEV environment...'
            }

            post {
        success {
            echo "Successfully deployed ${env.APP_NAME} to ${params.DEPLOY_ENV}!"
        }
        failure {
            echo "Deployment of ${env.APP_NAME} failed on build ${env.BUILD_NUMBER}!"
        }
        always {
            echo 'Pipeline finished.'
        }
    }
        }

        stage('Deploy to Staging') {
            when {
                expression { params.DEPLOY_ENV == 'staging' }
            }
            steps {
                echo 'Deploying to STAGING environment...'
            }
        }

        stage('Deploy to Production') {
            when {
                allOf {
                    expression { params.DEPLOY_ENV == 'production' }
                    branch 'main'
                }
            }
            steps {
                echo 'Deploying to PRODUCTION...'
            }
        }
        stage('Not first build') {
    when {
        expression { env.BUILD_NUMBER.toInteger() > 1 }
    }
    steps {
        echo "This is build number ${env.BUILD_NUMBER}"
    }
}

    }

    post {
        success {
            echo "Successfully deployed ${env.APP_NAME} to ${params.DEPLOY_ENV}!"
        }
        failure {
            echo "Deployment of ${env.APP_NAME} failed on build ${env.BUILD_NUMBER}!"
        }
        always {
            echo 'Pipeline finished.'
        }
        always {
            cleanWs()    // also clean up after the build finishes
        }
    }
    
}
