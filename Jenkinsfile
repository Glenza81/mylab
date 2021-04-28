pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }
    environment{
        GroupId = readMavenPom().getGroupId()
        ArtifactId = readMavenPom().getArtifactId()
        Version = readMavenPom().getVersion()
    }

    stages {
        // Specify various stage with in stages

        // stage 1
        stage ('Build'){
            steps {
                sh 'mvn clean install'
            }
        }

        // Stage2
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }

        // Stage3
        stage ('Sonarqube Analysis') {
            steps {
                echo ' Source code published to Sonarqube for Static Code Analysis'
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn sonar:sonar'
                }

            }
        }

        // Stage : Print some information
        // stage ('Print Environment variables'){
        //     steps {
        //         echo "GroupID is '${GroupId}'"
        //         echo "Artifact ID is '${ArtifactId}'"
        //         echo "Version is '${Version}'"
        //         }
        // }

        // Stage4 : Publish artifacts to Nexus
        stage ('Publish to Nexus'){
            steps {
                script {
                    
                    def NexusRepo = Version.endsWith("SNAPSHOT") ? "DevOpsLab-SNAPSHOT" : "DevOpsLab-RELEASE"

                    nexusArtifactUploader artifacts: 
                    [[artifactId: "${ArtifactId}", 
                    classifier: '', 
                    file: "target/${ArtifactId}-${Version}.war", 
                    type: 'war']], 
                    credentialsId: '06f37ef0-094c-4c62-96b5-1e30190f4a99', 
                    groupId: "${GroupId}", 
                    nexusUrl: '172.20.10.142:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: "${NexusRepo}", 
                    version: "${Version}"
                }
            }
        }

        // Stage5
        stage ('Deploy to Tomcat'){
            steps {
                echo "Deploying to tomcat ..."
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'Ansible-Controller', 
                            transfers: [
                                sshTransfer(
                                    cleanRemote: false, 
                                    execCommand: 'ansible-playbook /opt/playbooks/download_deploy_to_tomcat.yaml -i /opt/playbooks/hosts', 
                                    execTimeout: 120000, 
                                    flatten: false, 
                                    makeEmptyDirs: false, 
                                    noDefaultExcludes: false, 
                                    patternSeparator: '[, ]+', 
                                    remoteDirectory: '', 
                                    remoteDirectorySDF: false, 
                                    removePrefix: '', 
                                    sourceFiles: '')
                            ], 
                            usePromotionTimestamp: false, 
                            useWorkspaceInPromotion: false, 
                            verbose: false)
                        ])
                }
        }

        // Stage6
        stage ('Deploy to Docker'){
            steps {
                echo "Deploying to docker ..."
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'Ansible-Controller', 
                            transfers: [
                                sshTransfer(
                                    cleanRemote: false, 
                                    execCommand: 'ansible-playbook /opt/playbooks/download_deploy_to_docker.yaml -i /opt/playbooks/hosts', 
                                    execTimeout: 120000 
                                )
                            ], 
                            usePromotionTimestamp: false, 
                            useWorkspaceInPromotion: false, 
                            verbose: false)
                        ])
                }
        }

        // Stage3 : Publish the source code to Sonarqube
        // stage ('Sonarqube Analysis'){
        //     steps {
        //         echo ' Source code published to Sonarqube for SCA......'
        //         withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
        //              sh 'mvn sonar:sonar'
        //         }

        //     }
        // }

        
        
    }

}