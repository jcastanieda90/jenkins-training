#!groovy

pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

	environment {
		KUBECONFIG = "C:\\Users\\FRANCISCOJAVIERCASTA\\.bluemix\\plugins\\container-service\\clusters\\ibm-dsg\\kube-config-hou02-ibm-dsg.yml"
	}

    stages {

        stage("Build") {
            steps { buildApp() }
		}

        stage("Deploy - Dev") {
            steps { deploy('dev') }
		}

    stage('Slack Message') {
        steps {
            slackSend channel: '#jenkins-dsg',
                color: '#FFFF00',
                message: "*${currentBuild.currentResult}:* @here Deployment to production environment muts be approved by @fcastaneda ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"

        }
    }

        stage("Approve") {
            steps { approve() }
		}

        stage("Deploy - Live") {
            steps { deploy('live') }
		}

	  }
}

def buildApp() {
  try
  {
	   def appImage = docker.build("fcastaneda/dockerapp:lastest")//${BUILD_NUMBER}
     appImage.push("lastest")
  }
  catch (Exception e)
  {
    slackSend channel: '#jenkins-dsg',
        color: '#danger',
        message: "*${currentBuild.currentResult}:* @here Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
  }
}


def deploy(env) {
  if("${env}" == 'dev')
  {
    try
    {
    dir('k8s')
    {
    	bat 'ibmcloud update -f'
    	bat 'ibmcloud login --apikey @C:\\Users\\FRANCISCOJAVIERCASTA\\Documents\\apikey.json -r us-south'
    	bat 'kubectl -n dev apply -f flask.yaml'
    	bat 'kubectl -n dev get all'
    }
  }
  catch (Exception e)
  {
    slackSend channel: '#jenkins-dsg',
        color: '#danger',
        message: "*${currentBuild.currentResult}:* @here Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
  }
  }
  else if("${env}" == 'live')
  {
    try
    {
      dir('k8s')
      {
      	bat 'ibmcloud update -f'
      	bat 'ibmcloud login --apikey @C:\\Users\\FRANCISCOJAVIERCASTA\\Documents\\apikey.json -r us-south'
      	bat 'kubectl -n live apply -f flask.yaml'
      	bat 'kubectl -n live get all'
        slackSend channel: '#jenkins-dsg',
        color: 'good',
        message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
      }
    }
    catch (Exception e)
    {
      slackSend channel: '#jenkins-dsg',
          color: '#danger',
          message: "*${currentBuild.currentResult}:* @here Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
    }
  }
}


def approve() {

	timeout(time:1, unit:'DAYS') {
		input('Do you want to deploy to live?')
	}
}
