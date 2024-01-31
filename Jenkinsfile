pipeline {
    agent any

    parameters {
        string(name: 'GITHUB_CREDENTIAL', defaultValue: 'github_cred', description: 'Github access credentials id')
        string(name: 'GITHUB_REPO_URL', defaultValue: 'https://github.com/essiendaniel33/project_ansible.git', description: 'Github repository url')
        string(name: 'GITHUB_BRANCH', defaultValue: 'master', description: 'Github branch for your build')
        string(name: 'ANSIBLE_CREDENTIAL', defaultValue: 'ansible_cred', description: 'Ansible access credentials id for servers')
        string(name: 'ANSIBLE_CRED_JENKINS', defaultValue: 'ansible_key_jenkins', description: 'Ansible access credentials id for Jenkins server')
        string(name: 'AWS_CREDENTIAL_ID', defaultValue: 'aws_cred', description: 'AWS credentials id')
    }

    environment {
        GITHUB_CREDENTIAL = "${params.GITHUB_CREDENTIAL}"
        GITHUB_REPO_URL = "${params.GITHUB_REPO_URL}"
        GITHUB_BRANCH = "${params.GITHUB_BRANCH}"
        ANSIBLE_CREDENTIAL = "${params.ANSIBLE_CREDENTIAL}"
        ANSIBLE_CRED_JENKINS = "${params.ANSIBLE_CRED_JENKINS}"
        AWS_CREDENTIAL_ID = "${params.AWS_CREDENTIAL_ID}"
    }
    
    
    stages {
        stage('Checkout') {
            steps {
                script {
                    // Clone the GitHub repository
                    checkout([$class: 'GitSCM', 
                              branches: [[name: "*/${GITHUB_BRANCH}"]], 
                              userRemoteConfigs: [[url: GITHUB_REPO_URL, credentialsId: GITHUB_CREDENTIAL]]])
                }
            }
        }

        stage('Run Ansible Playbook1') {
            steps {
                script {
                    // Run Ansible playbook
                    ansiblePlaybook(
                        playbook: 'playbook1.yml',
                        inventory: 'hosts',
                        credentialsId: 'private_key'
                    )
                }
            }
        }
        stage('Run Ansible Playbook2') {
            steps {
                script {
                    // Run Ansible playbook
                    ansiblePlaybook(
                        playbook: 'playbook2.yml',
                        inventory: 'hosts',
                        credentialsId: ANSIBLE_CRED_JENKINS
                    )
                }
            }
        }
    }
} 
