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
            
            copyArtifacts(projectName: 'Build-Pipeline');
            container('helm')
            {
                sh """helm init --client-only
                 helm repo add spring-repo https://eli-skaronea.github.io/helm-charts/
                 helm repo update
                 helm upgrade --install spring https://eli-skaronea.github.io/helm-charts/spring-chart-1.1-latest.tgz"""

            }
            withCredentials([usernamePassword(credentialsId: 'git-credentials', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) 
            {
                sh "git config user.name 'eli-skaronea'"
                sh "git config user.email 'eli.skaronea@gmail.com'"
                sh 'git add helm-charts'
                sh "git commit -m 'Jenkins pushed a new helm package'"
                sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/eli-skaronea/helm-charts.git HEAD:master"
            }
        } 
    }
}