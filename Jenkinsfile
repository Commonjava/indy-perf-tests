def params = []
def suitesDir = "suites"
def suiteNames = []
def builders = 2
def suiteYml = null

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
                        ]
                    )

                    echo "Got params: ${params}"
                    suiteYml = params[SUITE_YML]
                }
            }
        }
        stage('Run Test Builds') {
            parallel {
                stage("Builder 1") {
                    agent {
                        label 'python'
                    }
                    steps {
                        script {
                            echo "This is builder #1 out of ${builders}"
                            def suite = readYaml file: "${suitesDir}/${suiteYml}"
                            echo "Suite YAML parsed to:\n\n${suite}"

                            echo "Sleeping 15s"
                            sh script: "sleep 15"
                        }
                    }
                }
                stage("Builder 2") {
                    agent {
                        label 'python'
                    }
                    steps {
                        script {
                            echo "This is builder #2 out of ${builders}"
                            def suite = readYaml file: "${suitesDir}/${suiteYml}"
                            echo "Suite YAML parsed to:\n\n${suite}"

                            echo "Sleeping 15s"
                            sh script: "sleep 15"
                        }
                    }
                }
            }
        }
    }
}