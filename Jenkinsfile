node{
    
    def registryProject='registry.gitlab.com/youssouy/docker-gitlab'
    def IMAGE= "${registryProject}:compil-${env.BUILD_ID}"
    
    stage('Build - Clone'){
        git 'https://github.com/OusmanaTraore/war-build-docker.git'
    }
    stage ('Build - Maven'){
        sh 'mvn package'
    }
    
    def img = stage ('Docker - Build'){
        docker.build("$IMAGE", '.')
    }
    stage ('Build -Test'){
      img.withRun("--name run-$BUILD_ID -p 81:80") { 
        sh 'docker ps'
        sh 'netstat -ntaup'
        sh 'sleep 5s'
        sh 'curl 192.168.56.1:81'
        sh 'docker ps'
        }
    }
    stage ('Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push 'latest'
            img.push()
        } 
    }
    stage ('Deploy -Clone'){
        git 'https://github.com/OusmanaTraore/jenkins-ansible-docker-1.git'
    }
    stage ('Deploy - End'){
        ansiblePlaybook(
            playbook: "playbook.yml",
            inventory: "inventory.yml",
//            inventory: "${HOST},",
            extras: "--extra-vars 'image=$IMAGE'",
            colorized: true,
            become: true 
        
            )
    }
}
