#!groovy
properties([buildDiscarder(logRotator(numToKeepStr: '15')), pipelineTriggers([])])

try {
    timestamps {
        runCheckout()

        runPipeline()
    }

} catch(org.jenkinsci.plugins.workflow.steps.FlowInterruptedException ex){
    node {
        echo '============= Exception=' + ex.getMessage()
        echo '============= Exception class=' + ex.class.toString()
        if (ut != null) { ut.notifyBuild('ABORTED') }
    }
    throw ex
} catch(Exception ex){
    node {
        echo '============= Exception=' + ex.getMessage()
        echo '============= Exception class=' + ex.class.toString()
        if (ut != null ) { ut.notifyBuild('FAILURE', ex.getMessage(), true) }
    }
    error(ex.getMessage());
}

/*
    Checkout of source code, so the pipeline scripts may be loaded
    and pipelines run and puts it into stash
 */
def runCheckout() {
    node {
        stage('Checkout') {
            echo '============= BRANCH=' + BRANCH_NAME
            echo '============= BUILD NUMBER=' + BUILD_NUMBER
            deleteDir() // ensure folder is empty and no old files
            checkout scm

            // Load pipeline scripts into Global variables
            echo '=Loading pipeline scripts from ops-pipeline'

            echo 'stashing all sources'
            stash name: 'allsources', includes: '**/*'

            deleteDir() // ensure folder is empty and no old files
        }
    }
}

/*
    Based on branches starts relevant pipeline
 */
def runPipeline() {
     node {
        stage('Deploy to blue or green ?') {
            def environment = null
            def environmentCreds = null
            def envToRun = 'green'
            def userInput;
            timeout(time: 60, unit: 'MINUTES') {
                def choicePMC = choice(choices: "blue\ngreen\n", description: 'Deployment environment. Possible options are blue and green', name: 'env-to-run');
                userInput = input message: 'Which environment deploy the release ?', parameters: [choicePMC]
            }

            echo "Selected env ${userInput['env-to-run']}"

            envToRun = userInput['env-to-run']

            environment = 'bg' + envToRun
            environmentCreds = 'pmc'

            echo "Deploying to ${environment}"
        }
     }
}
