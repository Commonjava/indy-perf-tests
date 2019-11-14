def suite="indy.yml"
def builders=6


def cmName = 'indy-perf-tester-suites'
def suitesDir = "suites"
def templateFile="indy-perf-tester-template.json"

def suiteNames = []
def suites = [:]

pipeline {
    agent { label 'python' }
    stages {
        stage('Create/Start Performance Test Environment') {
            steps {
                script {
                    def foundFiles = findFiles(glob: "${suitesDir}/*.yml")
                    foundFiles.each{
                        def fname = it.name
                        suiteNames << fname
                        suites[fname] = readYaml(file: "${suitesDir}/${fname}")
                    }

                    openshift.withCluster() {
                        openshift.withProject() {
                            echo "Reading template: ${templateFile}"
                            def templateJson = readJSON(file: templateFile)
                            echo "Read template:\n\n${templateJson}"


                            def objs = openshift.process(templateJson, "-p", "SUITE_YML=${suite}", "BUILDERS=${builders}", "JOB_NAME=${env.BUILD_NAME}")
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

