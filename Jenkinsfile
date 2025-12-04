pipeline {
    agent { label 'slave2' }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'feature-1', url: 'https://github.com/pradeepreddy-hub/news-app-devops.git'
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
        stage('Push the artifacts into JFrog Artifactory') {
    steps {
        script {
            // Current timestamp
            def currentDate = new java.text.SimpleDateFormat("yyyy-MM-dd_HH-mm").format(new Date())

            // Repository + folder path
            def targetPath = "my-repo-local/pradeep.devops.releases/${currentDate}/"

            // Upload artifact to Artifactory
            rtUpload(
                serverId: "jfrog",
                spec: """
                {
                    "files": [
                        {
                            "pattern": "${env.WAR_FILE}",
                            "target": "${targetPath}"
                        }
                    ]
                }
                """
            )
        }
    }
}

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
