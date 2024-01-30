pipeline {
    agent any

    environment {
        ANSIBLE_HOME = tool 'YourAnsibleInstallation'
    }

    stages {
        stage('Run Ansible Playbook') {
            steps {
                script {
                    ansiblePlaybook(
                        playbook: '/home/ec2-user/project_ansible/playbook1.yml',
                        inventory: '/home/ec2-user/project_ansible/hosts',
                        credentialsId: 'ansible_cred'
                    )
                }
            }
        }
    }
}

