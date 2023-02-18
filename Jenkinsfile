node {

    stage('Git checkout'){
        git 'https://github.com/Ajayab05/Kubernetes_Project.git'
    }
    stage( 'Sending docker file to ansible over ssh'){
        sshagent(['ansible_demo']) {
          sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 ' 
          sh 'scp /var/lib/jenkins/workspace/pipline-demo/* ubuntu@172.31.22.158:/home/ubuntu'
      }
    stage ("Docker a docker image"){
       sshagent(['ansible_demo']) {
          sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 cd /home/ubuntu/'
          sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 docker image build -t $JOB_NAME:v1.$BUILD_ID .'
    }
    }
    stage('Docker image tagging'){
        sshagent(['ansible_demo']) {
          sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 cd /home/ubuntu/'
          sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 docker image tag  $JOB_NAME:v1.$BUILD_ID ajayab05/$JOB_NAME:v1.$BUILD_ID '
          sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 docker image tag  $JOB_NAME:v1.$BUILD_ID ajayab05/$JOB_NAME:latest '
    } 
    }
    stage(" Pushing to the dockerhub"){
        sshagent(['ansible_demo']) {
            withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                                sh "ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 docker login -u ajayab05 -p ${dockerPassword}"
                                sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 docker image push ajayab05/$JOB_NAME:v1.$BUILD_ID '
                                sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 docker image push ajayab05/$JOB_NAME:latest '}
                                sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 docker image rm ajayab05/$JOB_NAME:v1.$BUILD_ID ajayab05/$JOB_NAME:latest '
    }
    }
    }    
    stage("Copy files to ansible to Kubernetes server"){
        sshagent(['kubernates_server']) {
            sh 'ssh -o StrictHostKeyChecking=no  ubuntu@43.204.217.154'
           sh 'scp /var/lib/jenkins/workspace/pipline-demo/* ubuntu@43.204.217.154:/home/ubuntu/'

        }
    }
    stage("kubernates deployment using ansible") {
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 cd /home/ubuntu/'
            sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158 ansible -m ping node'
            sh 'ssh -o StrictHostKeyChecking=no  ubuntu@172.31.22.158  ansible-playbook ansible.yml'
 
    }

}
     