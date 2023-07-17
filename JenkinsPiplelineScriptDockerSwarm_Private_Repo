node{
    def buildNumber = BUILD_NUMBER
    stage("Git CheckOut"){
        git url: 'https://github.com/bansshikavin/java-web-app-docker.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "mavenTool", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
    } 
    
    stage("Build Dokcer Image") {
         sh "docker build -t avadaipparvathy/java-web-app:${buildNumber} ."
    }
    
    stage("Docker Push"){
        withCredentials([string(credentialsId: 'Docker_Hub_Pwd', variable: 'Docker_Hub_Pwd')]) {
          sh "docker login -u avadaipparvathy -p ${Docker_Hub_Pwd}"
        }
        sh "docker push avadaipparvathy/java-web-app:${buildNumber}"
        
    }
    stage("Deploy to dockercontainer in docker deployer"){
        sshagent(['docker_ssh_password']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.91.15 docker service rm -f cloudcandy || true'
            sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.91.15 docker run -d -p 8080:8080 --name cloudcandy avadaipparvathy/java-web-app:${buildNumber}"
        }
}
}
