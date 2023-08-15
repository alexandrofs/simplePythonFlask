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
    }

    stages{
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