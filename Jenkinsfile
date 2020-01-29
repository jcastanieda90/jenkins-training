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

        stage("Approve") {
            steps { approve() }
		}

        stage("Deploy - Live") {
            steps { deploy('live') }
		}
	}
}

def buildApp() {
	def appImage = docker.build("fcastaneda/dockerapp:lastest")//${BUILD_NUMBER}
  appImage.push("fcastaneda/dockerapp:lastest")
}


def deploy(env) {
  if("${env}" == 'dev')
  {
    dir('k8s')
    {
    	bat 'ibmcloud update -f'
    	bat 'ibmcloud login --apikey @C:\\Users\\FRANCISCOJAVIERCASTA\\Documents\\apikey.json -r us-south'
    	bat 'kubectl -n dev apply -f flask.yaml'
    	bat 'kubectl -n dev get all'
    }
  }
  else if("${env}" == 'live')
  {
    dir('k8s')
    {
    	bat 'ibmcloud update -f'
    	bat 'ibmcloud login --apikey @C:\\Users\\FRANCISCOJAVIERCASTA\\Documents\\apikey.json -r us-south'
    	bat 'kubectl -n live apply -f flask.yaml'
    	bat 'kubectl -n live get all'
    }
  }
}


def approve() {

	timeout(time:1, unit:'DAYS') {
		input('Do you want to deploy to live?')
	}
}
