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

        stage('Build new helm package') 
        {
            
            container('helm')
            {
                sh "helm init --client-only"
                sh """
                   helm package spring-app/ --version 1.0-${env.BUILD_NUMBER} -d docs/
                   helm package spring-app/ --version 1.0-latest -d docs/
                   helm repo index docs/ --url https://eli-skaronea.github.io/helm-charts/
                  """ 
            }
            withCredentials([usernamePassword(credentialsId: 'git-credentials', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) 
            {
                sh """git config user.name 'eli-skaronea'
                git config user.email 'eli.skaronea@gmail.com'
                git add docs
                """
                def commitMessage = "'Jenkins pushed spring-consumer-1.0-" + buildNumber + " and latest'"
                sh "git commit -m " + commitMessage
                sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/eli-skaronea/helm-charts.git HEAD:master"
                
            }
        } 
    }
}