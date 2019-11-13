def suiteYml = "indy.yml"
def concurrency=2
def templateFile="/mnt/indy-test-templates/indy-perf-tester-template.yml"


pipeline {
    agent { label 'python' }
    stages {
        stage('Create/Start Performance Test Environment') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            echo "Reading template: ${templateFile}"
                            def objs = openshift.process(readFile(file: templateFile), "-p", "SUITE_YML=${suiteYml}", "BUILDERS=${concurrency}")
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

