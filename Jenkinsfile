pipeline {
    agent none

    stages {
        stage('Checkout') {
            agent { label 'worker-a' }
            steps {
                git branch: 'main', url: 'https://github.com/<your-username>/hello-maven.git'
                stash name: 'source'
            }
        }

        stage('Build in Parallel') {
            parallel {
                stage('Build on Worker-A') {
                    agent { label 'worker-a' }
                    steps {
                        unstash 'source'
                        sh 'mvn clean package'
                        archiveArtifacts artifacts: 'target/hello-maven-1.0-SNAPSHOT.jar', fingerprint: true
                    }
                }
                stage('Build on Worker-B') {
                    agent { label 'worker-b' }
                    steps {
                        unstash 'source'
                        sh 'mvn clean package'
                        archiveArtifacts artifacts: 'target/hello-maven-1.0-SNAPSHOT.jar', fingerprint: true
                    }
                }
            }
        }

        stage('Run Program on Worker-A') {
            agent { label 'worker-a' }
            steps {
                sh 'java -jar target/hello-maven-1.0-SNAPSHOT.jar'
            }
        }
    }

    post {
        success {
            echo "✅ Built on both workers and ran successfully on worker-a!"
        }
        failure {
            echo "❌ Build failed — check logs."
        }
    }
}
