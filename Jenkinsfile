pipeline {
    agent{
        label "jenkins-agent"
    }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "complete-prodcution-e2e-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "kummer"
        DOCKER_PASS = 'dockerhub' 
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        // JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")

    }
    stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }

        }
    
        // stage("Checkout from SCM"){
        //     steps {
        //         git branch: 'main', credentialsId: 'github', url: 'https://github.com/shmulik-kummer/complete-prodcution-e2e-pipeline'
        //     }

        // }

        // stage("Build Application"){
        //     steps {
        //         sh "mvn clean package"
        //     }

        // }

        // stage("Test Application"){
        //     steps {
        //         sh "mvn test"
        //     }

        // }
        
        // stage("Sonarqube Analysis") {
        //     steps {
        //         script {
        //             withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
        //                 sh "mvn sonar:sonar"
        //             }
        //         }
        //     }

        // }

        // stage("Quality Gate") {
        //     steps {
        //         script {
        //             waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
        //         }
        //     }

        // }

        // stage("Build & Push Docker Image") {
        //     steps {
        //         script {
        //             docker.withRegistry('',DOCKER_PASS) {
        //                 docker_image = docker.build "${IMAGE_NAME}"
        //             }

        //             docker.withRegistry('',DOCKER_PASS) {
        //                 docker_image.push("${IMAGE_TAG}")
        //                 docker_image.push('latest')
        //             }
        //         }
        //     }

        // }


        stage("Checkout GitOps Repo") {
            steps {
                dir("gitops") { // Checkout into a separate directory
                    git branch: 'main', credentialsId: 'github', url: "https://github.com/shmulik-kummer/devops-repo"
                }
            }
        }

        stage("Update Deployment File") {
                steps {
                    dir("gitops") {
                script {
                    // Assuming your Git remote is already configured to use HTTPS

                    sh "sed -i 's|${IMAGE_NAME}:.*|${IMAGE_NAME}:${IMAGE_TAG}|' deployment.yml"
                    sh "git config user.email 'kummersh@gmail.com'"
                    sh "git config user.name 'shmulik-kummer'"
                    sh "git add deployment.yml"
                    sh "git commit -m 'Update image tag to ${IMAGE_TAG}'"
                    // Perform Git operations; Jenkins will use configured credentials for HTTPS remote
                }
            }
        }
}
        


         stage("Push to Git Repository") {
    steps {
        // Ensure we are within the gitops directory to push
        dir("gitops") {
            withCredentials([string(credentialsId: 'github_secret', variable: 'GITHUB_TOKEN')]) {
                sh "git push https://${GITHUB_TOKEN}@github.com/shmulik-kummer/devops-repo.git main"
            }
        }
    }
}

        // stage("Trivy Scan") {
        //     steps {
        //         script {
		//    sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image dmancloud/complete-prodcution-e2e-pipeline:1.0.0-22 --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
        //         }
        //     }

        // }

        // stage ('Cleanup Artifacts') {
        //     steps {
        //         script {
        //             sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
        //             sh "docker rmi ${IMAGE_NAME}:latest"
        //         }
        //     }
        // }


    //     stage("Trigger CD Pipeline") {
    //         steps {
    //             script {
    //                 sh "curl -v -k --user admin:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'https://192.168.64.5:8080/job/gitops-complete-pipeline/buildWithParameters?token=gitops-token'"
    //             }
    //         }

    //     }

    // }

    // post {
    //     failure {
    //         emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
    //                 subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Failed", 
    //                 mimeType: 'text/html',to: "dmistry@yourhostdirect.com"
    //         }
    //      success {
    //            emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
    //                 subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Successful", 
    //                 mimeType: 'text/html',to: "dmistry@yourhostdirect.com"
    //       }      
    // }
}
}