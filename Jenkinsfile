pipeline{
    agent any

    tools{
        maven 'maven_3_5_0'
    }   

    environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerpasswd')
	} 

    parameters{
        gitParameter branchFilter: 'origin/(.*)', defaultValue: 'main', name: 'BRANCH', type: 'PT_BRANCH'
    }

    stages{
        stage("Java SCM Checkout"){
            steps{
                echo "========Java Code Checkout========"
                git branch: "${params.BRANCH}", url: 'https://github.com/akshayraina999/spring-boot-pipeline-project.git'
            }
        }
        // stage("UNIT Testing"){
        //     steps{
        //         echo "========UNIT Testing of Code========"
        //         sh 'mvn test'
        //     }
        // }
        stage("Compile"){
            steps{
                echo "========Compiling Code========"
                sh 'mvn clean install -DskipTests'
            }
        }
        stage("SCM for DevOps files"){
            steps{
                echo "========Source Code Checkout for DevOps files========"
                git credentialsId: 'github',
                    url: 'https://github.com/akshayraina999/website-k8.git'
            }
        }
        stage("Docker Image Build"){
            steps{
                echo "========Building Docker Image========"
                sh 'docker build . -t akshayraina/${JOB_NAME}:v.${BUILD_ID}'
            }
        }
        stage("Saving Image to DockerHub"){
            steps{
                echo "========Pushing Docker Image========"
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
    }
}