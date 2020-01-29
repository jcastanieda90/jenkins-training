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
	//dir ('/section_4/code/cd_pipeline' ) {
		def appImage = docker.build("fcastaneda/dockerapp:lastest")//${BUILD_NUMBER}
	//}
}


def deploy(environment) {
  dir('k8s')
  {
  	bat 'ibmcloud update -f'
  	bat 'ibmcloud login --apikey @C:\\Users\\FRANCISCOJAVIERCASTA\\Documents\\apikey.json -r us-south'
  	bat 'kubectl -n ${environment} apply -f flask.yaml'
  	bat 'kubectl -n ${environment} get all'
  }
}


def approve() {

	timeout(time:1, unit:'DAYS') {
		input('Do you want to deploy to live?')
	}

}
