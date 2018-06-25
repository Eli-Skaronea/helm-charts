podTemplate(label: 'deploypod', containers: 
    [
    containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true)
    ],
  volumes: 
  [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]
)

{
    node('deploypod')
    {
        stage('Checkout')
        {
            echo 'Checking out project repo...'
            checkout scm
        }

        stage('Deploy helm') 
        {
            def buildNumber = Jenkins.instance.getItem('Build-Pipeline').lastSuccessfulBuild.number
            copyArtifacts(projectName: 'Build-Pipeline');
            sh "cp helm-charts/docs/index.yaml docs"
            sh "cp helm-charts/docs/spring-chart-1.0-" + buildNumber + ".tgz docs"
            sh "cp helm-charts/docs/spring-chart-1.0-latest.tgz docs"
            
            container('helm')
            {
                sh """helm init --client-only
                 helm upgrade --install spring spring-chart-1.0-latest.tgz"""
            }
            withCredentials([usernamePassword(credentialsId: 'git-credentials', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) 
            {
                sh """git config user.name 'eli-skaronea'
                git config user.email 'eli.skaronea@gmail.com'
                git add docs
                """
                def commitMessage = "'Jenkins pushed spring-chart-1.0-" + buildNumber + " and latest'"
                sh "git commit -m " + commitMessage
                sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/eli-skaronea/helm-charts.git HEAD:master"
                
            }
        } 
    }
}