pipeline {

  environment {
    dockerimagename = "variantggg/aaa1"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Code') { 
      steps {
        git credentialsId: 'variantggg_dockerhub', url: 'https://github.com/udecdj2022/baymax.git', branch:'main'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhubhaep'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("vpipeline")
          }
        }
      }
    }

   stage('Restarting POD'){
   steps{
    sshagent(['digesetuserssh'])
    {
     sh 'scp -r -o StrictHostKeyChecking=no deployment-baymax.yaml digesetuser@148.213.1.133:/home/digesetuser/baymax'
      script{
        try{
           sh 'ssh digesetuser@148.213.1.133 microk8s.kubectl apply -f  /home/digesetuser/baymax/deployment-baymax.yaml --kubeconfig=/home/digesetuser/.kube/config'
           sh 'ssh digesetuser@148.213.1.133 microk8s.kubectl rollout restart deployment baymax -n baymax --kubeconfig=/home/digesetuser/.kube/config' 
           sh 'ssh digesetuser@148.213.1.133 microk8s.kubectl rollout status deployment baymax -n baymax --kubeconfig=/home/digesetuser/.kube/config'
          }catch(error)
       {}
     }
    }
  }
 }
}

  post{
            success{
            slackSend channel: 'prueba_pipeline_haep', color: 'good', failOnError: true, message: "${custom_msg()}", teamDomain: 'universidadde-bea3869', tokenCredentialId: 'slackpass' }
      }
 }

  def custom_msg()
  {
  def JENKINS_URL= "jarvis.ucol.mx:8080"
  def JOB_NAME = env.JOB_NAME
  def BUILD_ID= env.BUILD_ID
  def JENKINS_LOG= " DEPLOY LOG: Job [${env.JOB_NAME}] Logs path: ${JENKINS_URL}/job/${JOB_NAME}/${BUILD_ID}/consoleText"
  return JENKINS_LOG
  }
