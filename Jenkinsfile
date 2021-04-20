pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }
    environment {
        GroupId = readMavenPom().getGroupId()
        ArtifactId = readMavenPom().getArtifactId()
        Version = readMavenPom().getVersion()
        Name = readMavenPom().getName()
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install'
            }
        }

        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }

        // Stage3 : Publish artifacts to Nexus
        stage ('Publish to Nexus'){
            steps {
                nexusArtifactUploader artifacts: 
                [[artifactId: "${ArtifactId}", 
                classifier: '', 
                file: 'target/DevOpsLab-0.0.3-SNAPSHOT.war', 
                type: 'war']], 
                credentialsId: '06f37ef0-094c-4c62-96b5-1e30190f4a99', 
                groupId: "${GroupId}", 
                nexusUrl: '172.20.10.142:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'DevOpsLab-SNAPSHOT', 
                version: "${Version}"
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