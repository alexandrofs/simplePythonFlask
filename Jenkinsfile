pipeline {

    agent any

    options {
        timestamps()
        timeout(time:10, unit:'MINUTES')
    }

    environment {

        IMAGE_NAME="course_catalog"
        IMAGE_TAG="0.${BUILD_ID}"
        CONTAINER_IMAGE="${IMAGE_NAME}:${IMAGE_TAG}"
        HTTP_PROTOCOL="http://"
        NEXUS_REPOSITORY="192.168.56.20:8082"
        DOCKER_REGISTRY="${HTTP_PROTOCOL}${NEXUS_REPOSITORY}"

    }
    
    stages{

        stage('Dockerbuild') {

            steps {
                sh 'docker build -t "${CONTAINER_IMAGE}" .'
                sh 'docker tag "${CONTAINER_IMAGE}" "${NEXUS_REPOSITORY}/${CONTAINER_IMAGE}"'
            }

        }

        stage('Unit Testing'){
            steps{
                script{
                    image = docker.build("${CONTAINER_IMAGE}")
                    image.inside("-v ${WORKSPACE}:/courseCatalog/"){
                        sh "nosetests --with-xunit --with-coverage --cover-package=project test_users.py"
                    }
                }
            }
        }

        stage('Nexus - Saving Artifact'){
            steps{
                script{
                    docker.withRegistry("${DOCKER_REGISTRY}", 'b5a16469-3988-453b-a2a7-0bed11ba9d19'){
                        sh 'docker push "${NEXUS_REPOSITORY}/${CONTAINER_IMAGE}"'
                    }
                }
            }
        }        
    }

    post {
        success {
            echo "Ending pipeline with a SUCCESS status"
        }
        failure {
            echo "Ending pipeline with a FAILURE status"
        }
        cleanup {
            sh "docker image rm ${CONTAINER_IMAGE}"
        }
    }
}