@Library('my-shared-library') _

pipeline{
    // agent any
    agent {
        label 'gcp'
    }

    parameters{

        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        string(name: 'ImageName', description: "name of the docker build", defaultValue: 'javapp')
        string(name: 'ImageTag', description: "tag of the docker build", defaultValue: 'v1')
        string(name: 'DockerHubUser', description: "name of the Application", defaultValue: 'vikashashoke')
    }

    stages{
         
        stage('Git Checkout'){
                    when { expression {  params.action == 'create' } }
            steps{
            cleanWs()
            gitCheckout(
                branch: "main",
                url: "https://github.com/vidalgithub/mrdevops_java_app.git"
            )
            }
        }

          // stage('Select java 8 for maven test '){
          //       when { expression {  params.action == 'create' } }
          //   steps{
    
          //       script{
          //           sh '''
          //           selection=3
          //           echo "$selection" | sudo update-alternatives --config java
          //           '''
          //       }
          //   }
          //   } 

    
        //  stage('Unit Test maven'){
         
        //  when { expression {  params.action == 'create' } }

        //     steps{
        //        script{
                   
        //            mvnTest()
        //        }
        //     }
        // }
        //  stage('Integration Test maven'){
        //  when { expression {  params.action == 'create' } }
        //     steps{
        //        script{
                   
        //            mvnIntegrationTest()
        //        }
        //     }
        // }

          // stage('Select java 17 for sonarqube staticTest '){
          //       when { expression {  params.action == 'create' } }
          //   steps{
    
          //       script{
          //           sh '''
          //           selection=0
          //           echo "$selection" | sudo update-alternatives --config java
          //           '''
          //       }
          //   }
          //   }
        
        stage('Static code analysis: Sonarqube'){
         when { expression {  params.action == 'create' } }
            steps{
              catchError(message:'Even if SonarAnalisis fails.') {
               script{
                   
                   def SonarQubecredentialsId = 'sonarqube-token'
                   statiCodeAnalysis(SonarQubecredentialsId)
               }
            }
            }
        }
        stage('Quality Gate Status Check : Sonarqube'){
         when { expression {  params.action == 'create' } }
            steps{
              catchError(message:'Even if QualityGate fails.') {
               script{
                   
                   // def SonarQubeServer = 'sonarqube-9.9'
                   QualityGateStatus()
               }
            }
            }
        }
        stage('Maven Build : maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   mvnBuild()
               }
            }
        }
        stage('Docker Image Build'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerBuild("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }
         stage('Docker Image Scan: trivy '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImageScan("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }
        stage('Docker Image Push : DockerHub '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImagePush("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }   
        stage('Docker Image Cleanup : DockerHub '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImageCleanup("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }

        // post {
        //     // Clean after build
        //     always {
        //         cleanWs(cleanWhenNotBuilt: false,
        //                 deleteDirs: true,
        //                 disableDeferredWipeout: true,
        //                 notFailBuild: true,
        //                 patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
        //                            [pattern: '.propsfile', type: 'EXCLUDE']])
        //     }
        // }
        
    }
}
