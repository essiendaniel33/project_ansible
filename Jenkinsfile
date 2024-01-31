pipeline {
    agent any

    parameters {
        string(name: 'GITHUB_CREDENTIAL', defaultValue: 'github_cred', description: 'Github access credentials id')
        string(name: 'GITHUB_REPO_URL', defaultValue: 'https://github.com/essiendaniel33/project_ansible.git', description: 'Github repository url')
        string(name: 'GITHUB_BRANCH', defaultValue: 'master', description: 'Github branch for your build')
        string(name: 'REMOTE_USER', defaultValue: 'ec2-user', description: 'Remote server username')
        string(name: 'REMOTE_PATH', defaultValue: '/home/ec2-user/project_ansible', description: 'Remote path')
    }

    environment {
        GITHUB_CREDENTIAL = "${params.GITHUB_CREDENTIAL}"
        GITHUB_REPO_URL = "${params.GITHUB_REPO_URL}"
        GITHUB_BRANCH = "${params.GITHUB_BRANCH}"
        REMOTE_USER = "${params.REMOTE_USER}"
        REMOTE_PATH = "${params.REMOTE_PATH}"
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
                    def remotePath = '/home/ec2-user/project_ansible'
                    def gitRepoURL = 'https://github.com/essiendaniel33/project_ansible.git' 
                    def deployCommand = "scp -i $JENKINS_HOME"
                    
                    // Checkout the repository
                    checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: gitRepoURL]]])

                    // Copy playbook1.yml
                    deployCommand += " ${workspace}/playbook1.yml ${ansibleUser}@${ansibleServer}:${remotePath}/"

                    // Copy playbook2.yml
                    deployCommand += " ${workspace}/playbook2.yml ${ansibleUser}@${ansibleServer}:${remotePath}/"

                    // Copy hosts file
                    deployCommand += " ${workspace}/hosts ${ansibleUser}@${ansibleServer}:${remotePath}/"

                    sshPublisher(
                        continueOnError: false,
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'deployment',
                                transfers: [
                                    sshTransfer(
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
                                    )
                                ],
                                usePromotionTimestamp: false,
                                useWorkspaceInPromotion: false,
                                verbose: true
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
        
