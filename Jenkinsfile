#!/usr/bin/groovy

@Library(['github.com/indigo-dc/jenkins-pipeline-library@1.0.0']) _

pipeline {
    agent {
        label 'docker-build'
    }

    environment {
        dockerhub_repo = "deephdc/tensorflow"
        tf_ver = "1.12.0"
    }

    stages {
        stage('Docker image building') {
            when {
                anyOf {
                   branch 'master'
                   buildingTag()
               }
            }
            steps{
                checkout scm
                script {
                    // build different tags
                    id = "${env.dockerhub_repo}"

                    // CPU + ubuntu18.04 (no CUDA)
                    sh "docker build --no-cache --force-rm -t ${id}:${tf_ver}-py36 \
                        -f ./dockerfiles/cpu.Dockerfile \
                        --build-arg UBUNTU_VERSION=18.04 \
                        --build-arg TF_PACKAGE=tensorflow \
                        --build-arg TF_PACKAGE_VERSION=${tf_ver} \
                        --build-arg USE_PYTHON_3_NOT_2=yes ."

                    // GPU + ubuntu18.04 (CUDA 9.0)
                    sh "docker build --no-cache --force-rm -t ${id}:${tf_ver}-gpu-py36 \
                        -f ./dockerfiles/gpu.Dockerfile \
                        --build-arg UBUNTU_VERSION=18.04 \
                        --build-arg CUDA=9.0 \
                        --build-arg TF_PACKAGE=tensorflow-gpu \
                        --build-arg TF_PACKAGE_VERSION=${tf_ver} \
                        --build-arg USE_PYTHON_3_NOT_2=yes ."
                }
            }
            post {
                failure {
                    DockerClean()
                }
            }
        }

        stage('Docker Hub delivery') {
            when {
                anyOf {
                   branch 'master'
                   buildingTag()
               }
            }
            steps{
                script {
                    DockerPush(dockerhub_repo) // should push all tags
                }
            }
            post {
                failure {
                    DockerClean()
                }
                always {
                    cleanWs()
                }
            }
        }
    }
}
