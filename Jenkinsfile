// ECS Build definition with docker for Jenkins



// // groovy scripted style...
// node(label: ''){
//     stage('build'){

//     }
//     stage('test'){

//     }
//     stage('publish'){
        
//     }
//     stage('deploy'){

//     }
// }



// declarative style...
pipeline {
    agent {label 'test-worker'}                                         // restrict build to workers with this label    

    // tools {} //TODO: provide tools for usage in pipeline stages

    // options {}

    environment {                                                       // create custom environment variables
        // SOME_ENV_VAR = 'foo'
        // SOME_CREDENTIAL = credentials('someCredentialID')            // import a credential/secret defined in jenkins configuration

        // Setup aws environment variables with created jenkins credentials
        AWS_ACCESS_KEY_ID       = credentials('tests-aws-access-key-id')
        AWS_SECRET_ACCESS_KEY   = credentials('tests-aws-secret-access-key')
        AWS_DEFAULT_REGION      = 'us-west-1'

        // Setup custom env variables
        AWS_ACCOUNT_ID          = sh(script: 'echo "$(aws sts get-caller-identity --query Account --output text)"', returnStdout: true).trim()
        GIT_REPOSITORY_NAME     = env.GIT_URL.replaceFirst(/^.*\/([^\/]+?).git$/, '$1')

        // Setup env variables for the build
        ECR_URI                 = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
        ECR_REPOSITORY_NAME     = "example_ecr_repository" // "${GIT_REPOSITORY_NAME}_${env.GIT_BRANCH}_example_ecr_repository"
        ECS_CLUSTER_NAME        = "example_ecs_cluster"    // "${GIT_REPOSITORY_NAME}_${env.GIT_BRANCH}_example_ecs_cluster"
        ECS_SERVICE_NAME        = "example_ecs_service"    // "${GIT_REPOSITORY_NAME}_${env.GIT_BRANCH}_example_ecs_service"
        // ECS_CONTAINER_NAME      = "${GIT_REPOSITORY_NAME}_${env.GIT_BRANCH}_example_ecs_container"
        // IMAGE_TAG               = 'latest'
    }

    stages{
        stage('prebuild'){
            steps{                
                sh 'aws --version'
                sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${ECR_URI}"
            }
        }
        stage('build'){
            steps{
                sh "docker build -t ${GIT_REPOSITORY_NAME}:latest ./application"
                sh "docker tag ${GIT_REPOSITORY_NAME}:latest ${ECR_URI}/${ECR_REPOSITORY_NAME}:latest"                
            }
        }
        stage('test'){            
            steps{
                echo 'no tests'
            }
        }
        stage('publish & deploy'){
            when {                                                      // set conditions to execute stage
                expression {                    
                    BRANCH_NAME == 'main' || BRANCH_NAME == 'master'    // Jenkins native environment variables: <JENKINS_HOST:8080>/env-vars.html/
                } 
            }
            steps{
                sh "docker push ${ECR_URI}/${ECR_REPOSITORY_NAME}:latest"   
                sh "aws ecs update-service --cluster ${ECS_CLUSTER_NAME} --service ${ECS_SERVICE_NAME} --force-new-deployment"             
            }
        }       
    }

    // post {                                                           // conditionally run scripts after stages            
    //     always {
    //        
    //     }
    //     success {
    //        
    //     }
    //     failure {
    //        
    //     }
    // }
}
