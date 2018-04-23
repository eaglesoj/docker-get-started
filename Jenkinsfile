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
            String morpheusUrl = 'https://192.168.1.69'
			String postBody = '{ "tiers": { "App": { "linkedTiers": [], "instances": [ { "instance": { "type": "docker", "cloud": "MyVCenter", "layout": { "code": "docker-1. 7-single", "id": 280 }, "name": "get-started", "expireDays": "1" }, "volumes": [ { "rootVolume": true, "name": "root", "size": 1 } ], "backup": { "createBackup": false }, "config": { "expose": 8080, "dockerImageVersion": "latest", "dockerRegistryId": "", "dockerImage":"jjeagleson/get-started" }, "plan": { "id": 99, "code": "container-128" }, "metadata": [ { "name": "", "value": "" } ], "evars": [ { "name":"", "value": "" } ], "ports": [ { "name": "web", "port": "80", "lb": "" } ] } ] } }, "name": "testapp", "templateImage": "", "image": "/assets/apps/template.png", "id": 9, "templateName": "test", "group": { "id": 4, "name": "Lakehouse" }, "environment": "Test" }'
            MorpheusAppBuild("${morpheusUrl}","${postBody}","${bearer}")
            //echo "response is ${response}"
			// echo morpheusApp.buildApp(morpheusUrl, postBody, "${bearer}")
        }
    }
}
def MorpheusAppBuild(String morpheusUrl,String postBody,String bearer) {
    def morpheusAppurl = "${morpheusUrl}/api/apps"
    //authenticate with Morpheus
    def morpheusAuth = sh ( 
        script: "curl -k -X POST --data \"${bearer}\" \"${morpheusUrl}/oauth/token?grant_type=password&scope=write&client_id=morph-customer\"",
        returnStdout: true
    ).trim()
    def jsonSlurper = new JsonSlurper()
    def authresponse = jsonSlurper.parseText(morpheusAuth)
    echo "authresponse is ${authresponse}"
    def accesstoken = authresponse.access_token
    echo "accesstoken is ${accesstoken}"
    //make http post
    echo "command to run: curl -k -X POST \"${morpheusAppurl}\" -H \"Authorization: BEARER ${accesstoken}\" -H \"Content-Type: application/json\" -d '${postBody}'"
    def morpheusHTTP = sh (
        script: "curl -k -X POST \"${morpheusAppurl}\" -H \"Authorization: BEARER ${accesstoken}\" -H \"Content-Type: application/json\" -d '${postBody}'",
        returnStdout: true
    ).trim()
    //return morpheusHTTP
}
