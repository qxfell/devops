node('xianfei-jnlp') {
    stage('Prepare') {
        echo "1.Prepare Stage"
        checkout scm
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
            }
        }
    }
    stage('Test') {
      echo "2.Test Stage"
    }
    stage('Build') {
        echo "3.Build Docker Image Stage"
        sh "docker build -t registry.cn-beijing.aliyuncs.com/xianfei-devops/jenkins-pipeline:${build_tag} ."
    }
    stage('Push') {
        echo "4.Push Docker Image Stage"
        withCredentials([usernamePassword(credentialsId: 'dockerAliyun', passwordVariable: 'dockerAliyunPassword', usernameVariable: 'dockerAliyunUser')]) {
            sh "docker login -u ${dockerAliyunUser} -p ${dockerAliyunPassword} registry.cn-beijing.aliyuncs.com "
            sh "docker push registry.cn-beijing.aliyuncs.com/xianfei-devops/jenkins-pipeline:${build_tag}"
        }
    }
    stage('Deploy') {
        echo "5. Deploy Stage"
        if (env.BRANCH_NAME == 'master') {
            input "确认要部署线上环境吗？asdasdasdasdasdasd"
        }
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"
        sh "kubectl apply -f k8s.yaml --record"
        sh "kubectl get pods -n kube-ops"
        echo "This is Master!"
    }
}
