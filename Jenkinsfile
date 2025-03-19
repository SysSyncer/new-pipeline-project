pipeline {
    agent any

    environment {
        GIT_CRED_ID = 'github-project'  // Ensure this credential exists in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: GIT_CRED_ID, usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                        git branch: 'main', url: "https://${GIT_USER}:${GIT_TOKEN}@github.com/PadmavathyNarayanan/jenkins-pipeline-demo.git"
                    }
                }
            }
        }

        stage('Build & Test All Branches') {
            steps {
                script {
                    def branches = ['develop', 'main']

                    for (branch in branches) {
                        echo "Processing Branch: ${branch}"

                        withCredentials([usernamePassword(credentialsId: GIT_CRED_ID, usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                            checkout([$class: 'GitSCM', 
                                branches: [[name: "*/${branch}"]], 
                                userRemoteConfigs: [[url: "https://${GIT_USER}:${GIT_TOKEN}@github.com/PadmavathyNarayanan/jenkins-pipeline-demo.git"]]
                            ])
                        }

                        try {
                            sh 'mvn clean test'
                            echo "Build & Test Successful for ${branch}"
                        } catch (Exception e) {
                            echo "Build & Test Failed for ${branch}"
                        }
                    }
                }
            }
        }

        stage('Deploy if Main Branch') {
            when {
                expression { env.GIT_BRANCH == 'main' } // Check correct branch name
            }
            steps {
                echo "Deploying Application from Main Branch"
                sh 'scp target/app.jar user@server:/deployments/' // Ensure SSH setup
            }
        }
    }
}
