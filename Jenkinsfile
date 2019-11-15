def params = []
def suitesDir = "suites"
def suiteNames = []
def parallelStages = [:]

def generateStage(idx) {
    return {
        stage("Build ${idx}") {
            agent {
                label 'python'
            }
            steps {
                script {
                    unstash 'params'
                    echo "This is builder #${idx}"
                    sh script: "sleep 15"
                }
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
                    def suite = readFile(file: "${suitesDir}/${params.SUITE_YML}")

                    parallelStages = [:]
                    for(idx in 1..params.BUILDERS) {
                        stash name: params + "." + idx, suite: suite, builders: params.BUILDERS, builder: idx 

                        def builderName = "Builder ${idx}"
                        parallelStages[builderName] = generateStage(idx)
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