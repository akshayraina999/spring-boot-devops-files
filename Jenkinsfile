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
                    url: 'https://github.com/akshayraina999/spring-boot-devops-files.git'
            }
        }
        stage("Docker Image Build"){
            steps{
                echo "========Building Docker Image========"
                sh 'docker build . -t akshayraina/${JOB_NAME}:v1.${BUILD_ID}'
            }
        }
        stage("Saving Image to DockerHub"){
            steps{
                echo "========Pushing Docker Image========"
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push akshayraina/$JOB_NAME:v1.$BUILD_ID'
                sh 'docker rmi akshayraina/$JOB_NAME:v1.$BUILD_ID'
            }
        }
        stage("Transferring files to Ansible Server"){
            steps{
                echo "========Transferring files to Ansible Server========"
                sshagent(['ansible_server']){
                    sh 'ssh -o StrictHostKeyChecking=no root@10.83.191.88 cd /home/ubuntu/spring-boot-websocket/'
                    sh 'scp /var/lib/jenkins/workspace/${JOB_NAME}/playbook.yml root@10.83.191.88:/home/ubuntu/spring-boot-websocket/'
                }
            }
        }
        stage("Transferring files to Kubernetes Server"){
            steps{
                echo "========Transferring files to Kubernetes Server========"
                sshagent(['kubernetes_server']){
                sh 'ssh -o StrictHostKeyChecking=no akshay@172.16.3.58 cd /home/pristyn/spring-boot-websocket/'
                // sh 'mkdir -p /home/pc/${JOB_NAME}/' 192.168.1.88
                sh 'scp /var/lib/jenkins/workspace/${JOB_NAME}/deploy.yml akshay@172.16.3.58:/home/pristyn/spring-boot-websocket/'
                sh "ssh -o StrictHostKeyChecking=no akshay@172.16.3.58 sed -i 's/build_number/${BUILD_ID}/' /home/pristyn/spring-boot-websocket/deploy.yml"
                }
            }
        }
        stage("Deploy on Kubernetes"){
            steps{
                echo "========Deploying on Kubernetes Server========"
                sshagent(['ansible_server']){
                    sh 'ssh -o StrictHostKeyChecking=no root@10.83.191.88 ansible-playbook /home/ubuntu/spring-boot-websocket/playbook.yml'
                }
            }
        }
    }
}

// def lastSuccessfulBuildNo = Jenkins.instance.getItem(env.JOB_NAME).lastSuccessfulBuild.buildNumber
// echo "Last Success Build Name: ${lastSuccessfulBuildNo}"
