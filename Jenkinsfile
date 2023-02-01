pipeline{
    agent any

    tools{
        maven 'maven_3_5_0'
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
        stage("UNIT Testing"){
            steps{
                echo "========UNIT Testing of Code========"
                sh 'mvn test'
            }
        }
        stage("Compile"){
            steps{
                echo "========Compiling Code========"
                sh 'mvn clean install -DskipTests'
            }
        }
    }
}