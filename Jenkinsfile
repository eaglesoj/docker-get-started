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
            String morpheusUrl = 'https://192.168.1.69/api/apps'
			
			sh "curl -k -X POST --data \"${bearer}\" \"https://192.168.1.69/oauth/token?grant_type=password&scope=write&client_id=morph-customer\" > curl.out"
			def response = readFile 'curl.out'
			def jsonSlurper = new JsonSlurper()
			def authresponse = jsonSlurper.parseText(response)
			echo "authresponse is ${authresponse}"
			
			def accesstoken = authresponse.access_token
			echo "accesstoken is ${accesstoken}"
			
            postBody = "{ \"tiers\": { \"App\": { \"linkedTiers\": [], \"instances\": [ { \"instance\": { \"type\": \"docker\", \"cloud\": \"MyVCenter\", \"layout\": { \"code\": \"docker-1.7-single\", \"id\": 280 }, \"name\": \"get-started\", \"expireDays\": \"1\" }, \"volumes\": [ { \"rootVolume\": true, \"name\": \"root\", \"size\": 1 } ], \"backup\": { \"createBackup\": false }, \"plan\": { \"id\": 99, \"code\": \"container-128\" }, \"metadata\": [ { \"name\": \"\", \"value\": \"\" } ], \"evars\": [ { \"name\": \"\", \"value\": \"\" } ], \"config\": { \"expose\": 8080, \"dockerImageVersion\": \"latest\", \"dockerRegistryId\": \"\", \"dockerImage\": \"jjeagleson/get-started\" }, \"ports\": [ { \"name\": \"\", \"port\": \"\", \"lb\": \"\" } ] } ] } }, \"name\": \"testapp\", \"templateImage\": \"\", \"image\": \"/assets/apps/template.png\", \"id\": 9, \"templateName\": \"test\", \"group\": { \"id\": 4, \"name\": \"Lakehouse\" }, \"environment\": \"Test\" }"

			echo "bearer is ${bearer}"
			
			//def response = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', customHeaders: [[name: 'Authorization', value: "BEARER ${bearer}"]], requestBody: postBody, url: "http://192.168.1.69/api/apps"
			
			echo "curl -k -X POST \"${morpheusUrl}\" -H \"Authorization: BEARER ${accesstoken}\" -H \"Content-Type: application/json\" -d '${postBody}'"
			
			sh "curl -k -X POST \"${morpheusUrl}\" -H \"Authorization: BEARER ${accesstoken}\" -H \"Content-Type: application/json\" -d '${postBody}'"
			//def postresponse = readFile 'post.out'
			
			//echo "postresponse is ${postresponse}"
			
			// echo morpheusApp.buildApp(morpheusUrl, postBody, "${bearer}")
        }
    } 

}
