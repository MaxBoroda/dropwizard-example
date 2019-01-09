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
                }
            }
        }
        stage('CopyS3') {
            steps([
                $class: 'S3BucketPublisher',
                entries: [[
                    sourceFile: '**/*.jar',
                    bucket: '30daysdevops',
                    selectedRegion: 'us-east-1',
                    noUploadOnFailure: true,
                    managedArtifacts: true,
                    flatten: true,
                    showDirectlyInBrowser: true,
                    keepForever: true,
                ]],
                profileName: 'S3BucketPublisher',
                dontWaitForConcurrentBuildCompletion: false,
            ])
        }
    }
}