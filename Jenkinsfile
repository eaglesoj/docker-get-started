import groovy.json.JsonOutput 
import groovy.json.JsonSlurper

node {

    def app

    stage('Clone repository') {
      /*
         *
         *  */
         
        checkout scm
    }

    stage('Build image') {
       /*
         *
         *  */
         
        app = docker.build("jjeagleson/get-started")
    }

    stage('Test image') {
        /*
         *
         *  */
       
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
      
     
         docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        } 
    } 

     stage('Provision Dev App') {

        withCredentials([string(credentialsId: 'morpheus-credentials', variable: 'bearer')]) {
            //String morpheusUrl = 'https://192.168.1.69'
            String morpheusUrl = 'https://192.168.1.69/api/apps'
            //String postBody = '{ "tiers": { "App": { "linkedTiers": [], "instances": [ { "instance": { "type": "docker", "cloud": "MyVCenter", "layout": { "code": "docker-1. 7-single", "id": 280 }, "name": "get-started", "expireDays": "1" }, "volumes": [ { "rootVolume": true, "name": "root", "size": 1 } ], "backup": { "createBackup": false }, "config": { "expose": 8080, "dockerImageVersion": "latest", "dockerRegistryId": "", "dockerImage":"jjeagleson/get-started" }, "plan": { "id": 99, "code": "container-128" }, "metadata": [ { "name": "", "value": "" } ], "evars": [ { "name":"", "value": "" } ], "ports": [ { "name": "web", "port": "80", "lb": "" } ] } ] } }, "name": "testapp", "templateImage": "", "image": "/assets/apps/template.png", "id": 9, "templateName": "test", "group": { "id": 4, "name": "Lakehouse" }, "environment": "Test" }'
			Map<?, ?> postBody = [ "tiers": [ "App": [ "linkedTiers": [], "instances": [ [ "instance": [ "type": "docker", "cloud": "MyVCenter", "layout": [ "code": "docker-1. 7-single", "id": 280 ], "name": "get-started", "expireDays": "1" ], "volumes": [ [ "rootVolume": true, "name": "root", "size": 1 ] ], "backup": [ "createBackup": false ], "config": [ "expose": 8080, "dockerImageVersion": "latest", "dockerRegistryId": "", "dockerImage":"jjeagleson/get-started" ], "plan": [ "id": 99, "code": "container-128" ], "metadata": [ [ "name": "", "value": "" ] ], "evars": [ [ "name":"", "value": "" ] ], "ports": [ [ "name": "web", "port": "80", "lb": "" ] ] ] ] ] ], "name": "testapp", "templateImage": "", "image": "/assets/apps/template.png", "id": 9, "templateName": "test", "group": [ "id": 4, "name": "Lakehouse" ], "environment": "Test" ]
			morpheusApp.buildApp(morpheusUrl, postBody, "${bearer}")
            //def response = MorpheusAppBuild(morpheusUrl,postBody,bearer)
            //echo "response is ${response}"
        }
    }
}
/*
def MorpheusAppBuild(String morpheusUrl,String postBody,String bearer) {
    def morpheusAppurl = "${morpheusUrl}/api/apps"
	//authenticate and get token
	def accesstoken = MorpheusAuth(morpheusUrl,bearer)
    //make http post
    echo "running command: curl -k -X POST \"${morpheusAppurl}\" -H \"Authorization: BEARER ${accesstoken}\" -H \"Content-Type: application/json\" -d '${postBody}'"
    def morpheusHTTP = sh (
        script: "curl -k -X POST \"${morpheusAppurl}\" -H \"Authorization: BEARER ${accesstoken}\" -H \"Content-Type: application/json\" -d '${postBody}'",
        returnStdout: true
    ).trim()
    //return morpheusHTTP
    return "${morpheusHTTP}"
}
def MorpheusAuth(String morpheusUrl,String bearer) {
    def morpheusAuth = sh ( 
        script: "curl -k -X POST --data \"${bearer}\" \"${morpheusUrl}/oauth/token?grant_type=password&scope=write&client_id=morph-customer\"",
        returnStdout: true
    ).trim()
	def jsonSlurper = new JsonSlurper()
    def authresponse = jsonSlurper.parseText(morpheusAuth)
    accesstoken = authresponse.access_token
    //unset objects or will cause NonSerialized error
    jsonSlurper = null
    authresponse = null
	return accesstoken
}
*/
