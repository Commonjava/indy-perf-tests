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
        stage('Enter Parameters') {
            input(
                message "Please enter parameters for this test:"
                ok "Go"
                parameters(
                    choice(name: 'SUITE_YML', choices: suiteNames, description: "Test suite")
                )
            )

            steps {
                echo "Running: ${params.SUITE_YML}"
            }
        }
        // stage('Create/Start Performance Test Environment') {
        //     steps {
        //         script {
        //             def foundFiles = findFiles(glob: "${suitesDir}/*.yml")
        //             foundFiles.each{
        //                 def fname = it.name
        //                 suiteNames << fname
        //                 suites[fname] = readFile(file: "${suitesDir}/${fname}")
        //             }

        //             openshift.withCluster() {
        //                 openshift.withProject() {
        //                     echo "Reading template: ${templateFile}"
        //                     def templateJson = readJSON(file: templateFile)
        //                     templateJson['objects'][0]['data'] = suites

        //                     echo "Read+patched template:\n\n${templateJson}"


        //                     def objs = openshift.process(templateJson, "-p", "SUITE_YML=${suite}", "BUILDERS=${builders}", "JOB_NAME=${env.BUILD_TAG}")
        //                     echo "Got ${objs.size()} objects from processed template:\n\n${objs}"

        //                     def created = null
        //                     objs.each{
        //                         echo "Creating: ${it['kind']}:${obj['metadata']['name']}"
    
        //                         created = openshift.create(it)
    
        //                         created.withEach{
        //                             echo "Created ${it.kind()}:${it.name()}"
        //                         }
        //                     }
        //                 }
        //             }
        //         }
        //     }
        // }
    }
}

