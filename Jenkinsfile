pipeline {
    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(daysToKeepStr:'10', numToKeepStr: '10', artifactNumToKeepStr: '10', artifactDaysToKeepStr: '10' ))
    }    
    agent {
        docker {
            image 'mcr.microsoft.com/dotnet/sdk:5.0'
            // args '-v /tmp:/app -w /app -e DOTNET_CLI_HOME=/tmp/DOTNET_CLI_HOME'
            args '-v /tmp:/app -w /app -e DOTNET_CLI_HOME=/tmp/DOTNET_CLI_HOME -e PATH=$PATH:/tmp/DOTNET_CLI_HOME/.dotnet/tools'
        }
    }

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
            steps{
                sh "dotnet restore QuickApp.sln"
                sh "dotnet build QuickApp.sln --configuration Release"
            }
        }

        stage ('OWASP Dependency-Check Vulnerabilities') {
        agent {
                docker { 
                    image 'owasp/zap2docker-stable'
                    reuseNode true
                    args "-v $WORKSPACE/zap:/zap/wrk"
                }
        }
            steps {
               
                dependencyCheck additionalArguments: ''' 
                    -o "./"
                    -s "./"
                    -f "ALL"
                    --prettyPrint''', odcInstallation: "Dependency-Check"
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }  
        }

        stage('Unit Testing') {
            environment {
                allure = tool name: 'Allure'
            }
            agent {
                docker { 
                    image 'mcr.microsoft.com/dotnet/sdk:5.0'
                    // args '-v /tmp:/app -w /app -e DOTNET_CLI_HOME=/tmp/DOTNET_CLI_HOME'
                    args '-v /tmp:/app -w /app -e DOTNET_CLI_HOME=/tmp/DOTNET_CLI_HOME'
                }
            }
            steps {
                sh "dotnet test QuickApp.Tests/QuickApp.Tests.csproj -o target"
                // script {
                //     allure([
                //         includeProperties: false,
                //         jdk: '',
                //         properties: [],
                //         reportBuildPolicy: 'ALWAYS',
                //         results: [[path: 'target/allure-results']]])
                // }
            }
        }

        // stage('Sonarqube Scan') {            
        //     steps {
        //         withSonarQubeEnv('Sonarqube') {
        //             sh "dotnet tool install --global dotnet-sonarscanner"                    
        //             sh "dotnet sonarscanner begin /k:\"$PROJECT_NAME\" /o:\"$ORGANIZATION\" /d:sonar.branch.name=${env.BRANCH_NAME}"
        //             sh "dotnet build QuickApp.sln --configuration Release"
        //             sh "dotnet sonarscanner end"
        //             // sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION -Dsonar.projectKey=$PROJECT_NAME -Dsonar.branch.name=${env.BRANCH_NAME} -Dsonar.sources=."
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
