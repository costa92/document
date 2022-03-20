# 例子

```sh
String workspace = "/var/lib/jenkins/workspace/"

pipeline {
    agent any
    
    environment {
       BUILD_USER = ""
       EVN_FLAG = "NO"
       ITEM_PORT = 18310
    }
    options {
        timeout(time: 1, unit: 'HOURS') 
    }
    parameters {
        gitParameter branchFilter: 'origin/(.*)', defaultValue: 'test', name: 'BRANCH', type: 'PT_BRANCH'
    }
    stages {
        stage("准备") {
            steps{
                script {
                    ACTION = "${ServrtName}"
                }
            }
        }
        stage("拉取代码") {

            steps{
                echo "${params.BRANCH}"
                echo "start git pull"
                git branch: "${params.BRANCH}", credentialsId: '2d17c6d7-1cc8-4512-9101-47ad40b593df', url: 'git@192.168.11.161:business/go-server.git'
                script {
                  env.commit_id = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
                  env.image = sh(returnStdout: true, script: 'echo ${JOB_NAME}-${ServrtName}:${commit_id}').trim()
               }
            }
        }
        stage('编译打包API镜像') {
            when {
               equals expected: 'api',
               actual: ACTION
            }
            
            steps{
                sh 'cd /var/lib/jenkins/workspace/go-service-k8s/service/grid_address/cmd/api'
                sh 'echo 镜像名称：${image}'
                sh 'sudo docker build -f service/grid_address/cmd/api/Dockerfile -t ${image} .'        
            }
            
        }
        stage('编译打包MQ镜像') {
            when {
               equals expected: 'mq',
               actual: ACTION
            }
            
            steps{
                sh 'cd /var/lib/jenkins/workspace/go-service-k8s/service/grid_address/cmd/mq'
                sh 'echo 镜像名称：${image}'
                sh 'sudo docker build -f service/grid_address/cmd/mq/Dockerfile -t ${image} .'        
            }
            
        }
        
        stage("上传仓库") {
            steps{
                sh 'sudo docker tag ${image} 192.168.11.130/ml/grid-service-${ServrtName}:${commit_id}'
                sh 'sudo docker push 192.168.11.130/ml/grid-service-${ServrtName}:${commit_id}'
            }
        }
        
        stage("部署api到 k8s"){
            when {
               equals expected: 'api',
               actual: ACTION
            }
            steps{
                sh 'echo ks8'
                sh "sed -e 's#{APP_NAME}#grid-api#g;s#{IMAGE_TAG}#${commit_id}#g;s#{APP_PORT}#${ITEM_PORT}#g;s#{IMAGE_NAME}#grid-service-api#g' /home/dev/jenkins/zore-deployment-tpl.yaml > deployment.yaml"
                sh 'cat deployment.yaml'
                sh 'scp deployment.yaml root@192.168.11.102:/home'
                sh "ssh root@192.168.11.102 'kubectl apply -f /home/deployment.yaml' "
            }
        }
        
        stage("部署mq到 k8s"){
            when {
               equals expected: 'mq',
               actual: ACTION
            }
            steps{
                sh 'echo ks8'
                sh "sed -e 's#{APP_NAME}#grid-mq#g;s#{IMAGE_TAG}#${commit_id}#g;s#{IMAGE_NAME}#grid-service-mq#g' /home/dev/jenkins/zore-mq-deployment-tpl.yaml > deployment.yaml"
                sh 'cat deployment.yaml'
                sh 'scp deployment.yaml root@192.168.11.102:/home'
                sh "ssh root@192.168.11.102 'kubectl apply -f /home/deployment.yaml' "
            }
        } 
        
        
         stage("Clean"){
            steps{
                sh 'sudo docker rmi -f ${image}'
                sh 'sudo docker rmi -f 192.168.11.130/ml/grid-service-${ServrtName}:${commit_id}'
                sh 'ssh root@192.168.11.102 rm /home/deployment.yaml'
            }
        }
        
    }
}
```


根据 ServrtName 创建不同的服务类型
