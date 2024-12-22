pipeline {
    agent any
    triggers {
        pollSCM('*/1 * * * *')
    }
    stages {
        stage('build-docker-image') {
            steps {
                buildDockerImage()
            } 
        }
        stage('deploy-to-dev') {
            steps {
                deploy("dev")
            }
        }
        stage('tests-on-dev') {
            steps {
                runApiTests("dev")
            }
        }
        stage('deploy-to-stg') {
            steps {
                deploy("stg")
            }
        }
        stage('tests-on-stg') {
            steps {
                runApiTests("stg")
            }
        }
        stage('deploy-to-prod') {
            steps {
                deploy("prod")
            }
        }
        stage('tests-on-prod') {
            steps {
                runApiTests("prod")
            }
        }
    }
}

def buildDockerImage(){
    echo "Building docker image.."
    sh "docker build -t sandraosadcuka/python-greetings-app:latest ."

    echo "Pushing image to docker registry..."
    sh "docker push sandraosadcuka/python-greetings-app"
}

def deploy(String environment){
    echo "Pulling latest python-greetings-app from repository.."
    sh "docker pull sandraosadcuka/python-greetings-app:latest"
    
    echo "Deploying Python microservice to ${environment} environment.."
    sh "docker compose stop greetings-app-${environment}"
    sh "docker compose rm greetings-app-${environment}"
    sh "docker compose up -d greetings-app-${environment}"
}

def runApiTests(String environment){
    echo "API tests triggered on ${environment} env..."
    sh "docker pull sandraosadcuka/api-tests:latest"
    sh "docker run --network=host --rm sandraosadcuka/api-tests:latest run greetings greetings_${environment}"
}