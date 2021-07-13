node{
   stage('SCM Checkout'){
     git 'https://github.com/sumanthram348/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Imager'){
   sh 'docker build -t sumanthgunasani/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u sumanthgunasani -p ${dockerPassword}"
    }
   sh 'docker push sumanthgunasani/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.232.78.129:8083"
   sh "docker tag sumanthgunasani/myweb:0.0.2 13.232.78.129:8083/sumu:1.0.0"
   sh 'docker push 13.232.78.129:8083/sumu:1.0.0'
   }
   stage('Remove Previous Container'){
	    try{
	    	sh 'docker rm -f tomcattest'
	    }catch(error){
	    	//  do nothing if there is an exception
	    }
	    
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest sumanthgunasani/myweb:0.0.2' 
   }
}
}
