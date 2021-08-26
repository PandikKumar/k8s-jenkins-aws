node {
    stage("Git Clone"){

        git branch: 'main', credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/PandikKumar/k8s-jenkins-aws.git'
    }
    
    stage('Gradle Build') {
    sh './gradlew build'
}
   
   
    stage("Docker build"){
    sh 'docker version'
    sh 'docker build -t docker-demo .'
    sh 'docker image list'
    sh 'docker tag docker-demo pkjack/docker-demo:docker-demo'

}
withCredentials([string(credentialsId: 'Docker_HUB_PASSWORD', variable: 'PASSWORD')]) {
        sh 'docker login -u pkjack -p $PASSWORD'
    }
stage("Push Image to Docker Hub"){
        sh 'docker push  pkjack/docker-demo:docker-demo'
}

stage("Install kubectl"){
        sh 'curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.21.2/2021-07-05/bin/linux/amd64/kubectl'
        sh 'openssl sha1 -sha256 kubectl'
        sh 'chmod +x ./kubectl'
        sh 'mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin'
        
        
}

sshagent(['kops']) {
    // some block
    sh "scp -o StrictHostKeyChecking=no k8s-spring-boot-deployment.yml ec2-user@65.2.35.23:/home/ec2-user/"
    script{
        try{
            sh "ssh ec2-user@65.2.35.23 kubectl apply -f k8s-spring-boot-deployment.yml"
        }catch(error){
            sh "ssh ec2-user@65.2.35.23 kubectl create -f k8s-spring-boot-deployment.yml"
        }
    }
}



}
