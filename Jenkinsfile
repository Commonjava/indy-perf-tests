def suite="indy.yml"
def builders=6


def cmName = 'indy-perf-tester-suites'
def suitesDir = "suites"
def templateFile="indy-perf-tester-template.yml"

def suiteNames = []
def suites = [:]

pipeline {
    agent { label 'python' }
    stages {
        stage('Create/Start Performance Test Environment') {
            steps {
                script {
                    def foundFiles = sh(script: "ls -1 ${suitesDir}/*.yml", returnStdout: true).split()
                    foundFiles.each{
                        suiteNames << it
                        suites[it] = readYaml(file: "${suitesDir}/${it}")
                    }
                    
                    openshift.withCluster() {
                        openshift.withProject() {
                            echo "Deleting ConfigMap containing suite definitions"
                            openshift.selector("configmap", cmName).delete()

                            echo "Reading template: ${templateFile}"
                            def templateYml = readYaml(file: templateFile)
                            echo "Read template:\n\n${templateTxt}"


                            def objs = openshift.process(templateYml, "-p", "SUITE_YML=${suite}", "BUILDERS=${builders}", "JOB_NAME=${env.BUILD_NAME}")

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

