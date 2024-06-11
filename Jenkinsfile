pipeline{
    agent any
        stages{
            stage("k8s"){
                steps{
                    withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'cluster', contextName: '', credentialsId: 'kubernetes-token', namespace: 'default', serverUrl: 'https://7B880F51F2F08F0AB429BE4513791448.sk1.eu-west-2.eks.amazonaws.com']]) 
                    {
                    sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'
                    sh 'chmod u+x ./kubectl'
                    sh './kubectl get nodes'
                    sh './kubectl delete deployments --all && ./kubectl delete svc --all'
                    sleep 30
                    sh './kubectl create -f backend.yaml'
                    sh './kubectl create -f frontend.yaml'
                    sleep 30
                    sh './kubectl get pods'
                    sh './kubectl get svc'
                    script {
                        def externalIp = sh(script: './kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].hostname}"', returnStdout: true).trim()
                        echo "The external IP is: ${externalIp}"
                        sh "curl http://${externalIp}:5000"
                    }
                    }
                }    
        }
    }
}
