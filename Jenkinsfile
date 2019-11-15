def generateStage(builder, suiteYml, builders) {
    return {
        stage("Build " + builder) {
            agent {
                label 'python'
            }
            steps {
                script {
                    echo "This is builder #${builder} out of ${builders}"
                    def suite = readYaml string: suiteYml
                    echo "Suite YAML parsed to:\n\n${suite}"

                    echo "Sleeping 15s"
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
                    def params = []
                    def suitesDir = "suites"
                    def suiteNames = []

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
                    def suiteYml = readFile(file: "${suitesDir}/${params.SUITE_YML}")

                    def parallelStages = [:]
                    for(idx in 1..params.BUILDERS) {
                        // stash name: "params." + idx, suite: suite, builders: params.BUILDERS, builder: idx 

                        def builderName = "Builder ${idx}"
                        parallelStages[builderName] = generateStage(idx, suiteYml, params.BUILDERS)
                    }

                    parallel parallelStages
                }
            }
        }
    }
}