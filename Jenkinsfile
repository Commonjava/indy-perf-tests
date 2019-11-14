def cmName = 'indy-perf-tester-suites'
def suitesDir = "suites"
def templateFile="indy-perf-tester-template.yml"

def suiteNames = []
def suites = [:]
new File(suitesDir).list().each{
    if (!new File(suitesDir, it).isDirectory() ) {
        suiteNames << it
        suites[it] = readYaml(file: "${suitesDir}/${it}")
    }
}

pipeline {
    agent { label 'python' }
    parameters {
        choice(name: 'SUITE_YML', choices: suiteNames, description: "Which performance test suite do you want to run?")
        string(name: 'BUILDERS', defaultValue: '6', description: "How many concurrent builds should we run?")
    }
    stages {
        stage('Create/Start Performance Test Environment') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            echo "Deleting ConfigMap containing suite definitions"
                            openshift.selector("configmap", cmName).delete()

                            echo "Reading template: ${templateFile}"
                            def templateYml = readYaml(file: templateFile)
                            echo "Read template:\n\n${templateTxt}"


                            def objs = openshift.process(templateYml, "-p", "SUITE_YML=${params.SUITE_YML}", "BUILDERS=${params.BUILDERS}", "JOB_NAME=${env.BUILD_NAME}")

                            objs.each{
                                it.describe()
                            }
                            def created = openshift.create(objs)

                            created.withEach{
                                echo "Created ${it.kind()}:${it.name()}"
                            }
                        }
                    }
                }
            }
        }
    }
}

