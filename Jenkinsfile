pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout([$class: 'GitSCM',
                      branches: [[name: '${GITHUB_PR_HEAD_SHA}']], // Zmień na odpowiednią gałąź
                      userRemoteConfigs: [[url: 'https://github.com/SRP-Platform/srp_platform.git']]]
            )
                }
                script {
                    // Pobierz SHA bieżącego commita
                    def sha = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()

                    // Ustaw status na "Pending" przy użyciu GitHub Plugin
                    step([
                        $class: 'GitHubCommitStatusSetter',
                        reposSource: [$class: 'ManuallyEnteredRepositorySource', url: 'https://github.com/SRP-Platform/srp_platform.git'],
                        contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'srp-check'],
                        commitShaSource: [$class: 'ManuallyEnteredShaSource', sha: sha],
                        statusResultSource: [$class: 'ConditionalStatusResultSource',
                                             results: [[$class: 'AnyBuildResult', state: 'PENDING', message: 'Build is in progress']]]
                    ])
                }
            }
        }
        stage('Building') {
            steps {
                script {
                    try {
                        echo 'Building the project...'
                        // Symulacja budowania (tu możesz dodać swoje kroki)
                        sh 'sleep 5'

                        // Ustaw status na "Success" po pomyślnym wykonaniu
                        def sha = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    } catch (Exception e) {
                        // W przypadku błędu ustaw status na "Failure"
                        def sha = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                        step([
                            $class: 'GitHubCommitStatusSetter',
                            reposSource: [$class: 'ManuallyEnteredRepositorySource', url: 'https://github.com/SRP-Platform/srp_platform.git'],
                            contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'srp-check'],
                            commitShaSource: [$class: 'ManuallyEnteredShaSource', sha: sha],
                            statusResultSource: [$class: 'ConditionalStatusResultSource',
                                                 results: [[$class: 'AnyBuildResult', state: 'FAILURE', message: 'Build failed']]]
                        ])
                        throw e // Ponownie zgłoś błąd, aby zatrzymać potok
                    }
                }
            }
        }
        stage('Finish') {
            steps {
                script {
                    def sha = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    step([
                            $class: 'GitHubCommitStatusSetter',
                            reposSource: [$class: 'ManuallyEnteredRepositorySource', url: 'https://github.com/SRP-Platform/srp_platform.git'],
                            contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'srp-check'],
                            commitShaSource: [$class: 'ManuallyEnteredShaSource', sha: sha],
                            statusResultSource: [$class: 'ConditionalStatusResultSource',
                                                 results: [[$class: 'AnyBuildResult', state: 'SUCCESS', message: 'Build completed successfully']]]
                        ])
                }
            }
        }
    }
}
