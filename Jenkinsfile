node {
    stage('Initialize') {
        echo "Starting pipeline..."
        def envName = 'staging'
        String buildId = UUID.randomUUID().toString()
        println "Build ID: ${buildId}"
    }

    stage('Checkout') {
        checkout scm
    }

    stage('Build') {
        def buildStatus = 'SUCCESS'
        try {
            echo "Compiling project with Maven..."
            sh 'mvn clean compile'
        } catch (e) {
            buildStatus = 'FAILURE'
            echo "Build failed: ${e.message}"
        }

        if (buildStatus == 'FAILURE') {
            error "Stopping pipeline due to build failure."
        }
    }

    stage('Test') {
        List<String> testPhases = ['test', 'verify'] // simulate unit + integration
        for (phase in testPhases) {
            echo "Running Maven phase: ${phase}..."
            sh "mvn ${phase}"
        }
    }

    stage('Package') {
        echo "Packaging application..."
        sh 'mvn package'
    }

    stage('Deploy') {
        Map<String, String> deployTargets = [
            staging   : 'staging.example.com',
            production: 'prod.example.com'
        ]

        def targetEnv = params.TARGET_ENV ?: 'staging'
        String targetHost = deployTargets[targetEnv]
        if (!targetHost) {
            error "Unknown target environment: ${targetEnv}"
        }

        echo "Deploying to ${targetEnv} at ${targetHost}"
        sh "scp target/*.jar user@${targetHost}:/opt/app/"
    }

    stage('Notify') {
        def shouldNotify = true
        if (shouldNotify) {
            echo "Sending notification to Slack..."
            // Fake notification command
            println "Notification: Build ${currentBuild.result} for ${envName}"
        }
    }
}

