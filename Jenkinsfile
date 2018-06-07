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
        stage('Deploy helm') 
        {
            sh "ls"
            copyArtifacts(projectName: 'Build-Pipeline');
            container('helm')
            {
                sh """helm init --client-only
                 helm repo add spring-repo https://eli-skaronea.github.io/helm-charts/
                 helm repo update
                 helm upgrade --install spring https://eli-skaronea.github.io/helm-charts/spring-chart-1.1-latest.tgz"""
            }
        } 
    }
}