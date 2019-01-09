pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    archiveArtifacts artifacts: '**/*.jar', fingerprint: true
                    junit 'target/surefire-reports/*.xml'
                    s3Upload consoleLogLevel: 'INFO', dontWaitForConcurrentBuildCompletion: false, pluginFailureResultConstraint: 'FAILURE', profileName: 'S3BucketPublisher', userMetadata: []
                }
            }
        }
    }
}