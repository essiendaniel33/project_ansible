pipeline {
    agent any

    parameters {
        string(name: 'GITHUB_CREDENTIAL', defaultValue: 'github_cred', description: 'Github access credentials id')
        string(name: 'GITHUB_REPO_URL', defaultValue: 'https://github.com/essiendaniel33/project_ansible.git', description: 'Github repository url')
        string(name: 'GITHUB_BRANCH', defaultValue: 'master', description: 'Github branch for your build')
        string(name: 'REMOTE_USER', defaultValue: 'ec2-user', description: 'Remote server username')
    }

    environment {
        GITHUB_CREDENTIAL = "${params.GITHUB_CREDENTIAL}"
        GITHUB_REPO_URL = "${params.GITHUB_REPO_URL}"
        GITHUB_BRANCH = "${params.GITHUB_BRANCH}"
        REMOTE_USER = "${params.REMOTE_USER}"
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

        stage('copy ansible files') {
            steps {
                script {
                    def ansibleServer = '18.208.164.132'
                    def ansibleUser = 'ec2-user'
                    def remotePath = '/project_ansible'

                    // Copy playbook1.yml
                    sshPublisher(
                        continueOnError: false,
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'deployment',
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'playbook1.yml',
                                        removePrefix: '',
                                        remoteDirectory: "${remotePath}/"
                                    )
                                ]
                            )
                        ]
                    )

                    // Copy playbook2.yml
                    sshPublisher(
                        continueOnError: false,
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'deployment',
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'playbook2.yml',
                                        removePrefix: '',
                                        remoteDirectory: "${remotePath}/"
                                    )
                                ]
                            )
                        ]
                    )

                    // Copy hosts file
                    sshPublisher(
                        continueOnError: false,
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'deployment',
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'hosts',
                                        removePrefix: '',
                                        remoteDirectory: "${remotePath}/"
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
       
            
        stage('Deploy To Remote Server') {
            steps {
                script {
                   def deployCommand = '''
    cd /home/${REMOTE_USER}/project_ansible
    ansible-playbook -i hosts playbook1.yml playbook2.yml
'''

                    sshPublisher(publishers: [sshPublisherDesc(
                        configName: 'deployment',
                        transfers: [sshTransfer(
                            cleanRemote: false,
                            execCommand: deployCommand,
                            execTimeout: 120000,
                            flatten: false,
                            makeEmptyDirs: false,
                            noDefaultExcludes: false,
                            patternSeparator: '[, ]+',
                            remoteDirectory: '',
                            remoteDirectorySDF: false,
                            removePrefix: '',
                            sourceFiles: ''
                        )],
                        usePromotionTimestamp: false,
                        useWorkspaceInPromotion: false,
                        verbose: true
                    )])
                }
            }
        }
    }
}
        
