pipeline {
    agent any

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
