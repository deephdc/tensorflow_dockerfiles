#!/usr/bin/groovy

@Library(['github.com/indigo-dc/jenkins-pipeline-library@1.0.0']) _

pipeline {
    agent {
        label 'docker-build'
    }

    environment {
        dockerhub_repo = "deephdc/tensorflow"
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

                    def build_args = [
                            ["1.10.0", "9.0", "7.0"], // tf_version, cuda_version, cudnn_version
                            ["1.12.0", "9.0", "7.0"],
                            ["1.14.0", "10.0", "7.4"]
                            ]

                    for (current_args in build_args) {

                        def (tf_ver, cuda_ver, cudnn_ver) = current_args

                        // CPU + ubuntu18.04 (no CUDA)
                        sh "docker build --no-cache --force-rm -t ${id}:${tf_ver}-py36 \
                            -f ./dockerfiles/cpu.Dockerfile \
                            --build-arg UBUNTU_VERSION=18.04 \
                            --build-arg TF_PACKAGE=tensorflow \
                            --build-arg TF_PACKAGE_VERSION=${tf_ver} \
                            --build-arg USE_PYTHON_3_NOT_2=yes ."

                        // GPU + ubuntu18.04 (CUDA + CuDNN)
                        sh "docker build --no-cache --force-rm -t ${id}:${tf_ver}-gpu-py36 \
                            -f ./dockerfiles/gpu.Dockerfile \
                            --build-arg UBUNTU_VERSION=18.04 \
                            --build-arg CUDA=${cuda_ver} \
                            --build-arg CUDNN=${cudnn_ver} \
                            --build-arg TF_PACKAGE=tensorflow-gpu \
                            --build-arg TF_PACKAGE_VERSION=${tf_ver} \
                            --build-arg USE_PYTHON_3_NOT_2=yes ."
                    }
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
