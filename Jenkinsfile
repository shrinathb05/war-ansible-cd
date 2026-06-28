@Library('shared@main') _
pipeline {
    agent {
        label 'agent'
    }
    parameters {
        choice(name: 'ENV', choices: ['prod'], description: 'Environment')
    }

    environment {
        ANSIBLE_HOST_KEY_CHECKING = "False"
        GIT_REPO = "https://github.com/shrinathb05/webapp-java.git"
        GIT_BRANCH = "release"
    }
    
    stages {
        
        stage('Pre Deployment Cleanup') {
            steps {
                cleanWs()
            }
        }
        
        stage('Checkout & PRECheck') {
            steps {
                clone("https://github.com/shrinathb05/war-ansible-cd.git","release")
                sh 'ls -lrt'
                sh 'ansible-playbook playbooks/precheck.yml'
            }
        }
        
        stage('Download Artifacts') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-credentials-id',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh """
                        ansible-playbook -v playbooks/artifact_download.yml
                    """
                }
            }
        }
        
        stage('Backup') {
            steps {
                sh """
                    ansible-playbook -v playbooks/backup.yml
                """
            }
        }
        
        stage('Stop Service') {
            steps {
                sh """
                    ansible-playbook -v playbooks/tomcatstop.yml
                """
            }
        }
        
        stage('Deploy War') {
            steps {
                sh """
                    ansible-playbook -v playbooks/deploy_war.yml
                """
            }
        }
        
        stage('Start Service') {
            steps {
                sh """
                    ansible-playbook -v playbooks/start_tomcat.yml
                """
            }
        }
        
        stage('App Healthcheck') {
            steps {
                sh """
                    ansible-playbook -v playbooks/healthcheck.yml
                """
            }
        }
        
        stage('Post-Deployment-Cleanup') {
            steps {
                sh """
                    ansible-playbook -v playbooks/cleanup.yml
                """
            }
        }
    }
    post {
            success {
                     mail to: 'shrinath7028@gmail.com',
                     subject: "SUCCESS: Jenkins Build #${env.BUILD_NUMBER} - ${env.JOB_NAME}",
                     body: """Team,
    
                    The pipeline completed successfully!
                    
                    --------------------------------------------------
                    BUILD DETAILS
                    --------------------------------------------------
                    Job Name:      ${env.JOB_NAME}
                    Build Number:  #${env.BUILD_NUMBER}
                    Artifact:      01-maven-web-app
                    Version:       1.0.${env.BUILD_NUMBER}
                    Nexus Status:  Uploaded successfully to maven-releases
                    Build URL:     ${env.BUILD_URL}
                    
                    Regards,
                    Jenkins CI/CD Automation
                    """
            }
            
            failure {
                         mail to: 'shrinath7028@gmail.com',
                         subject: "FAILURE: Jenkins Build #${env.BUILD_NUMBER} - ${env.JOB_NAME}",
                         body: """Team,
            
                    The pipeline build has FAILED.
                    
                    --------------------------------------------------
                    FAILURE DETAILS
                    --------------------------------------------------
                    Job Name:      ${env.JOB_NAME}
                    Build Number:  #${env.BUILD_NUMBER}
                    Log URL:       ${env.BUILD_URL}console
                    
                    Please check the console logs to investigate the failure.
                    
                    Regards,
                    Jenkins CI/CD Automation
                """
        }
        
        always {
            echo "Cleaning up the Jenkins build workspace..."
            // This wipes out the build workspace directory on the execution agent
            cleanWs() 
        }
    }
}
