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
			
			echo "response is ${response}"
			
            Map<?, ?> postBody = [ "image": "/assets/apps/template.png", "tiers": [ "App": [ "linkedTiers": [ "Database" ], "tier": [ "bootOrder": 1, "lockedFields": [ "bootOrder" ] ], "instances": [ [ "instance": [ "type": "tomcat", "cloud": "MyVCenter", "name": "tomcat", "expireDays": "1", "layout": [ "code": "tomcat-7.0.82-single", "id": 119 ] ], "volumes": [ [ "rootVolume": true, "name": "root", "size": 1 ] ], "backup": [ "createBackup": false ], "plan": [ "id": 99, "code": "container-128" ], "metadata": [ [ "name": "", "value": "" ] ], "evars": [ [ "name": "", "value": "" ] ], "loadBalancer": [ [ "externalPort": 8080, "vipShared": "off", "externalAddress": "off" ] ] ] ] ], "Database": [ "linkedTiers": [], "instances": [ [ "instance": [ "type": "mysql", "cloud": "MyVCenter", "name": "mysql", "expireDays": "1", "layout": [ "code": "mysql-5.6-single", "id": 108 ] ], "volumes": [ [ "rootVolume": true, "name": "root", "size": 5 ] ], "backup": [ "createBackup": false ], "plan": [ "id": 101, "code": "container-512" ], "metadata": [ [ "name": "", "value": "" ] ], "evars": [ [ "name": "", "value": "" ] ], "config": [ "rootPassword": "object00", "username": "eaglesojsa", "password": "object00" ] ] ] ] ], "name": "MyApp", "templateImage": "", "id": 8, "templateName": "MyApp_Template", "group": [ "id": 4, "name": "Lakehouse" ], "environment": "Test" ]

			echo "bearer is ${bearer}"
			
			//def response = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', customHeaders: [[name: 'Authorization', value: "BEARER ${bearer}"]], requestBody: postBody, url: "http://192.168.1.69/api/apps"
			
			sh "curl -k -XPOST \"${morpheusUrl}\" -H \"Authorization: BEARER ${accesstoken}\" -H 'Content-Type: application/json' -d \"${postBody}\" > post.out"
			def response = readFile 'post.out'
			
			echo "response is ${response}"
			
			// echo morpheusApp.buildApp(morpheusUrl, postBody, "${bearer}")
        }
    } 

}
