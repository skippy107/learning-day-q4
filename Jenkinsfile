node {
    stage('Building application server image') {
        checkout scm
        docker.build("api-server")
    }

    stage('Launching server and running tests') {
        withDockerNetwork{ n ->
            docker.image('api-server').withRun("--network ${n} --name todo-api  -p 3000:3000") { c->
                docker.image('postman/newman').inside("--network ${n} -v ${WORKSPACE}:/etc/newman") {
                    sh "newman tests/learning-day.json -e tests/learning-day-env.json"
                }
            }
        }

    }
    
}
def withDockerNetwork(Closure inner) {
    try {
        networkId = UUID.randomUUID().toString()
        sh "docker network create ${networkId}"
        inner.call(networkId)
    } finally {
        sh "docker network rm ${networkId}"
    }
}


/*
***********************************************

create network
docker network create --driver bridge learning-net

create api server
docker build . -t api-server

run api server
docker run --network=learning-net -dit --name todo-api -p 3000:3000 api-server

run tests
docker run --network=learning-net -v c:\\users\\miket\\downloads\\learning-q4:/etc/newman \
            -t postman/newman run tests/learning-day.json \
            -e tests/learning-day-env.json

************************************************
*/

