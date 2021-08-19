// Build definition with docker for Jenkins(groovy)



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
    agent {label ''}            // restrict build to workers with this label

    environment {               // create custom environment variables
        SOME_ENV_VAR = 'foo'
        // SOME_CREDENTIALS = credentials('someCredential')   //TODO: requires plugin "Credentials Binding"
    }

    // tools {} //TODO: provide tools for usage in pipeline stages

    stages{
        stage('build'){
            steps{
                echo "build stage ${SOME_ENV_VAR}"
            }
        }
        stage('test'){            
            steps{
                echo 'test stage'
            }
        }
        stage('publish & deploy'){
            when {              // set conditions to execute stage
                expression {
                    // Jenkins native environment variables: <JENKINS_HOST:8080>/env-vars.html/
                    BRANCH_NAME == 'main' || BRANCH_NAME == 'master'
                } 
            }
            steps{
                echo 'publish & deploy stage'
            }
        }       
    }

    // post {
    //     always {
    //         // execute scripts independently from the results
    //     }
    //     success {
    //         // execute scripts when build succeeds
    //     }
    //     failure {
    //         // execute scripts when build fails
    //     }
    // }
}
