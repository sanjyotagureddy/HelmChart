def containerName="jenkinsproj"
def dockerHubUser="sanjyotagureddy"
def httpPort="8090"

node {

    stage('Checkout') {
        checkout scm
    }
	
    stage('Build'){
        sh "mvn clean install"
    }

    stage("Image Prune"){
         sh "docker image prune -f"
    }

    stage('Image Build'){
        sh "docker build -t $containerName:$tag  -t $containerName --pull --no-cache ."
        echo "Image build complete"
    }

    stage('Deploy to Kubernetes'){
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
            sh "docker login -u $dockerUser -p $dockerPassword"
			sh "kubectl get deployments"
			sh "helm list"
		sh "helm upgrade --install ${containerName} ./counterwebapp --set image.name=${dockerHubUser}/${containerName} --set image.tag=${tag} -f counterwebapp/values/dev/values.yaml"
	    sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
	    sh "docker push $dockerUser/$containerName:$tag"
            echo "Image push complete"
        }
    }
}
