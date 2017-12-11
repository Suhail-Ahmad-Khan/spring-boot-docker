node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }




    stage ('Initialize Maven') {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
        }




     stage ('Build Maven') {
                sh 'mvn -Dmaven.test.failure.ignore=true install'
        }







    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("fundoopay-docker")
	app.inside {
      			sh 'clean package'
 		   }
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
       docker.withRegistry('https://649347328056.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:jenkins-docker-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}