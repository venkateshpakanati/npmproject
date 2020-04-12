def label = "worker-${UUID.randomUUID().toString()}"

podTemplate(label: label, containers: [ 
  containerTemplate(name: 'node', image: 'node:9.11', command: 'cat', ttyEnabled: true,
  envVars: [envVar(key: 'NPM_CONFIG_USERCONFIG', value: '/data/.npm/config/.npmrc')])],  
  volumes: [
       configMapVolume(configMapName: 'npm-settings', mountPath: '/data/.npm/config'),         
       persistentVolumeClaim(claimName: 'npm-storage', mountPath: '/data/.npm')                
  ]
) {
  node(label) {
    
    stage('Checkout Code') {
       milestone ()
        // git branch: 'master',
        // credentialsId: '35205444-4645-4167-b50e-c65137059f09',
        // url: 'http://13.234.176.102/venkateshpakanati/mymicroservices.git'
        def myRepo = checkout scm
        def gitCommit = myRepo.GIT_COMMIT
        def gitLocalBranch = myRepo.GIT_LOCAL_BRANCH 
        def gitPrevCommit = myRepo.GIT_PREVIOUS_COMMIT
        def gitPrevSuccessCommit = myRepo.GIT_PREVIOUS_SUCCESSFUL_COMMIT
        def gitBranch = myRepo.GIT_BRANCH
        def shortGitCommit = "${gitCommit[0..10]}"
        def previousGitCommit = sh(script: "git rev-parse ${gitCommit}~", returnStdout: true)
        println "${gitCommit}   ${gitBranch}  ${shortGitCommit}  ${previousGitCommit}   ${gitLocalBranch}  ${gitPrevCommit} ${gitPrevSuccessCommit}"
      
        sh "ls -lat"
        stash name: "code-stash", includes: "**/*"
    }
    
    stage('Build Code') {
       milestone ()
       container('node') {
          unstash "code-stash"
          sh """
            pwd
            ls -lta
            node -v && npm -v && npm i
            npm run-script build           
            """
       }
    }   

  }
}
