pipeline {
    agent {
        label 'agent'
    }

    parameters {
        choice(name: 'ENV', choices: ['prod'], description: 'Environment')
    }

    environment {
        ANSIBLE_HOST_KEY_CHECKING = "False"
    }

    stages {

        stage('Checkout & PRE-CHECK') {
            steps {
                git branch: 'release', url: 'https://github.com/shrinathb05/war-ansible-cd.git'
            }
        }

        stage('PRE-CHECK') {
            steps {
                sh """
                    ansible-playbook playbooks/precheck.yml \
                    -e nexus_username=$NEXUS_USER \
                    -e nexus_password=$NEXUS_PASS \
                    -e env=${params.ENV}
                """
            }
        }

        stage('Download Artifacts') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-credentials-id',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    // sh '''
                    //     echo "Credentials injected"
                    //     export NEXUS_USERNAME=$NEXUS_USER
                    //     export NEXUS_PASSWORD=$NEXUS_PASS
                    // '''
                    sh """
                        ansible-playbook playbooks/artifact_download.yml \
                        -e nexus_username=$NEXUS_USER \
                        -e nexus_password=$NEXUS_PASS \
                        -e env=${params.ENV}
                    """
                }
            }
        }
        
    }

    post {
        success {
            echo "✅ Deployment Successful"
        }

        failure {
            echo "❌ Deployment Failed — check logs"
        }
    }
}
