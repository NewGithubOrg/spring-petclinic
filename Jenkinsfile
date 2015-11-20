

stage 'Dev'
node {
    checkout scm
    mvn 'clean package'
    archive 'target/x.war'
}

stage 'QA'
parallel(longerTests: {
    runTests(30)
}, quickerTests: {
    runTests(20)
})

stage name: 'Staging', concurrency: 1
node {
    deploy 'staging'
}

input message: "Does staging look good?"
try {
    checkpoint('Before production')
} catch (NoSuchMethodError _) {
    echo 'Checkpoint feature available in CloudBees Jenkins Enterprise.'
}

stage name: 'Production', concurrency: 1
node {
    deploy 'production'
    echo "Deployed to production"
}

def mvn(args) {
// tool name should be based on Jenkins Master Tool configuration
    sh "${tool 'mvn-3.3.3-x'}/bin/mvn ${args}"
}

def runTests(duration) {
    node {
        sh "sleep ${duration}"
    }
}

def deploy(id) {
    unarchive mapping: ['target/x.war' : 'x.war']
    sh "cp x.war /tmp/webapps/${id}.war"
    sh "sleep 10"
}

def undeploy(id) {
    sh "rm /tmp/webapps/${id}.war"
}

