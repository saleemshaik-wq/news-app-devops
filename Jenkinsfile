pipeline {
    agent { label 'slave2' }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'feature-1', url: 'https://github.com/saleemshaik-wq/news-app-devops.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests=false'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Check User') {
    steps {
        sh 'whoami'
    }
}
 // 6.3: Push the artifacts to Jfrog repository
stage('Push the artifacts into Jfrog Artifactory') {
    steps {
        script {
            // Get the current date and time in the format: yyyy-MM-dd_HH-mm
            def currentDate = new java.text.SimpleDateFormat("yyyy-MM-dd_HH-mm").format(new Date())

            // Define the target path with the timestamp
            def targetPath = "NewsApp/${currentDate}/"

            // Configure the Artifactory server
            rtServer(
                id: 'Artifactory',
                url: 'https://trialyth1ui.jfrog.io/artifactory',
                credentialsId: 'jfrog-credentials-id'   // must match Jenkins credentials
            )
        stage('Deploy WAR to Tomcat') {
            steps {
                sh '''
                    TOMCAT_PATH="/opt/tomcat10/webapps"
                    WAR_FILE="target/news-app.war"

                    echo "Cleaning old deployment..."
                    sudo rm -rf $TOMCAT_PATH/news-app $TOMCAT_PATH/news-app.war

                    echo "Copying new WAR..."
                    sudo cp $WAR_FILE $TOMCAT_PATH/

                    echo "Restarting Tomcat..."
                    pkill -f 'org.apache.catalina.startup.Bootstrap' || true
                    nohup $TOMCAT_PATH/../bin/startup.sh &
                '''
            }
        }
    }
    post {
        success {
            echo 'Build and deployment completed successfully!'
        }
        failure {
            echo 'Build or deployment failed. Check logs for details.'
        }
    }
}
