node {
    gitProjectURL = 'https://github.com/ozanbozkurtt/simple-spring-boot.git'
    containerName = 'simple-java'
    latestTag = 'latest'
    branchName = 'main'
    dockerProjectURL = 'https://github.com/ozanbozkurtt/dockerfiles-demo.git'
    dockerBranchName = 'main'
    dockerProjectName = 'Dockerfile'
    sonarProjectKey = 'simple-java'
    sonarLoginToken = 'sqp_18edf432e534ae4652cf09a17c6bbca952ae901d'
    sonarHostUrl = 'http://192.168.27.129:9000'
    stage('Get Dockerfile repository') {
        fileOperations([folderCreateOperation('mytmp')])
        fileOperations([folderCreateOperation('configserver')])
        dir('mytmp') {
            checkout([$class: 'GitSCM', branches: [[name: "${dockerBranchName}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[ url: "${dockerProjectURL}"]]])
        }
        
            sh("cp ../mytmp/${dockerProjectName} ./")
        
        fileOperations([folderDeleteOperation('mytmp')])
    }
    stage('Clone repository') {
        checkout([$class: 'GitSCM', branches: [[name: "${branchName}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[ url: "${gitProjectURL}"]]])
        
            withMaven(options:[artifactsPublisher(disabled: true)], globalMavenSettingsConfig: '30a69226-32ed-4962-9f5f-fd6b9096cb82', maven: 'maven', mavenOpts: '-DskipTests=true') {
            sh "mvn clean verify sonar:sonar -Dsonar.projectKey=${sonarProjectKey} -Dsonar.host.url=${sonarHostUrl} -Dsonar.login=${sonarLoginToken}"}
        
    }
    pom = readMavenPom file: 'pom.xml'
    VERSION = pom.version
    stage('Build & register') {
         def dockerHome = tool 'docker'
         env.PATH = "${dockerHome}/bin:${env.PATH}"
            sh("cp ../mytmp/${dockerProjectName} .")
        
            docker.withRegistry('https://192.168.27.129:5000') {
                def customImage = docker.build("${containerName}")
                customImage.push("${VERSION}")
                customImage.push("${latestTag}")
            
        }
    }
}
