node('master') {
 withMaven() {
  stage('Checkout') {
   git url: 'https://github.com/piomin/sample-spring-cloud-comm.git', credentialsId: 'github-piomin',   branch: 'feign_with_discovery'
  }

  stage('Build') {
   dir('account-service') {
    sh 'mvn clean install'
   }
   def pom = readMavenPom file:'pom.xml'
   print pom.version
   env.version = pom.version
   currentBuild.description = "Release: ${env.version}"
  }

  stage('Image') {
   dir ('account-service') {
    def app = docker.build "piomin/account-service:${env.version}"
    //app.push()
   }
  }

  stage ('Run') {
   docker.image("piomin/account-service:${env.version}").run('-p 8091:8091 -m 256M -e EUREKA_DEFAULT_ZONE=http://discovery:8761/eureka -d --name account --network sample-spring-cloud-network')
  }

 }
}
