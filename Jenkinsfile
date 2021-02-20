//define the groovy script
def gv

pipeline {
    agent any
    enviroment {
        MY_VERSION = '1.2'
        // environment variable can be used to extract the credentials and use it into code.
        // for credentials function to work it required to install plugin called 
        // "Credentails" and "Credential Binding"
        SERVER_CREDENTIALS = credentials('id-of-credential')
    }
    //When we required external parameter to build our pipeline
    parameters {
        string(name: 'BRANCH', defaultValue:'', description: '') 
        choice(name:'VERSION', choices: ['1.1.0', '1.2.0'])
        booleanParam(name: 'executeTests', defaultValue: true, description: '')
    }
    //when we required to use some kind of build tool 
    tools { 
        // currently there are three tools that jenkins file support 
        // maven, gradle and jdk
        maven 'Maven'
    }
    stages {
        stage('Intialization') {
            steps {
                echo 'This is intialization step'
                echo "Credentials is ${SERVER_CREDENTIALS}"
                // using of parameter 
                echo "Does execute test ${params.executeTests}"
            }
        }
        stage('Build') {
            steps {
                echo 'This is build step'
                // when requirement is to use credentials only in one step then it doesn't make sense to use the enviroment variable.
                withCredentails([
                    // usernameVariable and passwordVariable assign values to corresponding Variable.
                    usernamePassword(credentials:'id-of-credentials',usernameVariable: USER, passwordVariable: PWD)
                ]){
                    echo "can use user ${USER} and password ${PWD}"
                }
                // we can also use groovy script inside the step to make the code more readible.
                script { 
                    gv = load "script.groovy"
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'This is deploy step'
            }
        }
        stage('Test') {
            echo "enviroment variable ${MY_VERSION}"
            // conditional expression 
            when {
                expression {
                    BRANCH_NAME == 'dev' || MY_VERSION == '1.2'
                }
            }
            steps {
                script {
                    gv.testApp()
                }
                echo 'This is test step'
            }
        }
        stage('Release') {
            steps {
                echo 'This is release step'
            }
        }
    }
    post {
        // In the post section you can perform any task after all steps has been completed.
        // there are different conditions that can be used to take decision based on final status.
        always{
            // always execute even after deployment is failed. 
        }
        success { 
            // excute when deployment is successful.

        }
        failure {
            // execute when deployment is failed. for example : send mail to the team about the failure.
        }
    }

}
