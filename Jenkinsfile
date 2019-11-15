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

def parallelStages = [:]

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
                    def suiteYmlSrc = readFile(file: "${suitesDir}/${params.SUITE_YML}")

                    for(int i=1; i < Integer.parseInt(params.BUILDERS); i++) {
                        def idx = i
                        def builderName = "Builder ${idx}"
                        def suiteYml = suiteYmlSrc

                        parallelStages[builderName] = generateStage(idx, suiteYml, params.BUILDERS)
                    }
                }
            }
        }
        stage('Run Parallel Builds') {
            steps {
                script {
                    parallel parallelStages
                }
            }
        }
    }
}