def params = []
def suitesDir = "suites"
def suiteNames = []
def parallelStages = [:]

def generateStage(job) {
    return {
        stage("Build ${job}") {
            agent {
                label 'python'
            }
            steps {
                echo "This is ${job}"
                sh script: "sleep 15"
            }
        }
    }
}

pipeline {
    agent { label 'python' }
    stages {
        stage('Enter Parameters') {
            steps {
                script {
                    def foundFiles = findFiles(glob: "${suitesDir}/*.yml")
                    foundFiles.each{
                        suiteNames << it.name
                    }

                    echo "Got suite names: ${suiteNames}"
                    
                    params = input(
                        message: "Please enter parameters for this test:",
                        parameters:[
                            choice(name: 'SUITE_YML', choices: suiteNames, description: "Test suite"),
                            string(name: 'BUILDERS', defaultValue: '2', description: 'Number of concurrent builds')
                        ]
                    )
                    
                    echo "Running: ${params}"

                    parallelStages = [1..params.BUILDERS].collectEntries {
                        def builderName = "Builder ${it}"
                        [builderName: generateStage(builderName)]
                    }
                }
            }
        }
        stage('Run parallel stages') {
            steps {
                script {
                    parallel parallelStages
                }
            }
        }
    }
}