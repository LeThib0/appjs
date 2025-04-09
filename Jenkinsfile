pipeline {
	agent any

stages{
 //------------------------------------------------
  stage('Generate Local Tag') {
    steps {
        script {
            def shortHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
            def timestamp = new Date().format('yyyyMMddHHmmss')
            env.LOCAL_TAG = "v${timestamp}-${shortHash}"

            echo "Generated Local Tag: ${env.LOCAL_TAG}"
        }
    }
    }

 //------------------------------------------------

    stage('Docker Build & Push') {
        steps {
            withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD_', variable: 'DOCKER_HUB_PASSWORD')]) {
                echo "print tag : $LOCAL_TAG"
                sh 'docker login -u thib432 -p $DOCKER_HUB_PASSWORD'
                sh 'docker build -t thib432/appjs:$LOCAL_TAG .'
                sh 'docker push thib432/appjs:$LOCAL_TAG'
            }
        }
    }

 //------------------------------------------------

    stage(' Deployment docker ') {
        steps {
           
                script {
                    sh 'sudo docker ps -a --filter "name=thib432" --format "{{.ID}}" |sudo  xargs -r docker stop'
                    sh 'sudo docker ps -a --filter "name=thib432" --format "{{.ID}}" |sudo  xargs -r docker rm' 
                    sh 'docker run -d  -p 3000:80 --name thib432$LOCAL_TAG thib432/appjs:$LOCAL_TAG'
                }
           
        }
    }
//------------------------------------------------
}

}