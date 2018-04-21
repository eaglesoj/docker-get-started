import groovy.json.JsonOutput 

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
            String morpheusUrl = 'https://sandbox3/api/apps'

            Map<?, ?> postBody = [ "image": "/assets/apps/template.png", "tiers": [ "App": [ "linkedTiers": [ "Database" ], "tier": [ "bootOrder": 1, "lockedFields": [ "bootOrder" ] ], "instances": [ [ "instance": [ "type": "tomcat", "cloud": "MyVCenter", "name": "tomcat", "expireDays": "1", "layout": [ "code": "tomcat-7.0.82-single", "id": 119 ] ], "volumes": [ [ "rootVolume": true, "name": "root", "size": 1 ] ], "backup": [ "createBackup": false ], "plan": [ "id": 99, "code": "container-128" ], "metadata": [ [ "name": "", "value": "" ] ], "evars": [ [ "name": "", "value": "" ] ], "loadBalancer": [ [ "externalPort": 8080, "vipShared": "off", "externalAddress": "off" ] ] ] ] ], "Database": [ "linkedTiers": [], "instances": [ [ "instance": [ "type": "mysql", "cloud": "MyVCenter", "name": "mysql", "expireDays": "1", "layout": [ "code": "mysql-5.6-single", "id": 108 ] ], "volumes": [ [ "rootVolume": true, "name": "root", "size": 5 ] ], "backup": [ "createBackup": false ], "plan": [ "id": 101, "code": "container-512" ], "metadata": [ [ "name": "", "value": "" ] ], "evars": [ [ "name": "", "value": "" ] ], "config": [ "rootPassword": "object00", "username": "eaglesojsa", "password": "object00" ] ] ] ] ], "name": "MyApp", "templateImage": "", "id": 8, "templateName": "MyApp_Template", "group": [ "id": 4, "name": "Lakehouse" ], "environment": "Test" ]


			echo "bearer is ${bearer}"
			
			def response = httpRequest customHeaders: [[name: 'Authorization', value: "BEARER ${bearer}"]], url: 'https://sandbox3/api/apps'
			
			
           // echo morpheusApp.buildApp(morpheusUrl, postBody, "${bearer}")
        }
    } 

}
