pipeline {
    agent any
    environment {
        SCANNER_HOME = tool 'SonarScanner'
        ORGANIZATION = "devopsifyco"
        PROJECT_NAME = "demo-QuickApp"

        registry = "huyntt/demo-QuickApp"
        registryCredential = 'DockerHub'
        dockerImage = ''
        TAG         = ''
    }
   
    stages {
        stage('Build') {
            environment {
                HOME = '/tmp'
            } 
            agent {
                docker { 
                    image 'mcr.microsoft.com/dotnet/sdk:6.0'
                    args '-v $HOME/:/app -w /app'
                }
            }
            steps{
                sh "dotnet restore QuickApp.sln"
                sh "dotnet build QuickApp.sln --configuration Release"
            }
        }

        // stage('Unit Testing') {
        //      environment {
        //         allure = tool name: 'allure'
        //     }
        //     steps{
        //         catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
        //             bat "rmdir /s /q target"
        //             bat "dotnet test QuickApp.Tests/QuickApp.Tests.csproj -o target"  
        //         }
        //         script {
        //             allure([
        //                 includeProperties: false,
        //                 jdk: '',
        //                 properties: [],
        //                 reportBuildPolicy: 'ALWAYS',
        //                 results: [[path: 'target/allure-results']]])
        //              }
        //         }
        // }

        // stage("Statis Security Scans"){
        //     environment {
        //         scannerHome = tool name: 'sonar',type : 'hudson.plugins.sonar.SonarRunnerInstallation'
        //     }
        //     steps {
        //          withSonarQubeEnv("SonaQube") {
        //              bat "${scannerHome}/bin/sonar-scanner\
        //              -D sonar.projectKey=$ProjectCode\
        //              -D sonar.sources=.\
        //              -D sonar.login=$SonaQubeToken\
        //              -D sonar.host.url=http://localhost:9000/\
        //              -D sonar.version=$VERSION.$BUILD_ID"
        //         }
        //     }
        // }

        // stage("Composition Analysis Scans"){            
        //     steps {
        //         echo "Put you code to integrate with Blackduck or any other Composition Analysis Scans"
        //     }
        // }

        // stage("Dynamic Security Scans"){            
        //     steps {
        //         echo "Put you code to integrate with Veracode or any other Dynamic Security Scans"
        //     }
        // }
    }
}
