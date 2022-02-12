podTemplate(
    containers: [
    containerTemplate(name: 'node', image: "node:${env.FRONTEND_NODE_VERSION}", command: 'cat', ttyEnabled: true)
    ]){
    node(POD_LABEL) {
        properties(
            [
                buildDiscarder(logRotator(daysToKeepStr: '90', numToKeepStr: '')),
                disableConcurrentBuilds(),
            ]
        )
        cleanWs(
            deleteDirs: true,
            patterns: [
                [pattern: '**/node_modules/**', type: 'INCLUDE']
            ]
        )
        stage('Checkout Source') {
            checkout scm
        }
        withCredentials([
            string(
                credentialsId: "nexus_npm_auth_rw",
                variable: 'NPM_AUTH'
            )
        ]) {
            container('node') {
                stage('Setup NPM Registry') {
                    sh 'echo registry=${NEXUS_NPM_REGISTRY} > .npmrc'
                    sh 'echo "_auth=${NPM_AUTH}" >> .npmrc'
                }
                if (env.TAG_NAME) {
                    stage('Publish') {
                        sh 'npm --no-color publish'
                    }
                }
            }
        }
    }
}
