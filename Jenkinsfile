pipeline {
    agent none

    stages {
        stage('Checkout') {
            agent { label 'worker-a' }
            steps {
                git branch: 'main', url: 'https://github.com/TarunNagaSaiChadaram/devops_agent_worker_sample.git'
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
                        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                    }
                }
                stage('Build on Worker-B') {
                    agent { label 'worker-b' }
                    steps {
                        unstash 'source'
                        sh 'mvn clean package'
                        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                    }
                }
            }
        }

        stage('Run Program on Worker-A') {
            agent { label 'worker-a' }
            steps {
                sh 'java -jar target/*.jar'
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
