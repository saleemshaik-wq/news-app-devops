
pipeline {
    agent { label 'slave2' }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'feature-2', url: 'https://github.com/saleemshaik-wq/news-app-devops.git'
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
        6.3: Push the artifacts to Jfrog repository
stage('Push the artifacts into Jfrog Artifactory') {
    steps {
        script {
            stage('Push artifacts to JFrog Artifactory') {
    steps {
        script {
            // Generate timestamp folder
            def currentDate = new java.text.SimpleDateFormat("yyyy-MM-dd_HH-mm").format(new Date())

            // Target path inside the repo
            def targetPath = "NewsApp/${currentDate}/news-app.war"   // Change repo name if needed

            withCredentials([usernamePassword(credentialsId: 'jfrog-credentials-id',
                                             usernameVariable: 'JF_USER',
                                             passwordVariable: 'JF_PASS')]) {

                sh """
                    echo "Uploading WAR to Artifactory..."
                    curl -u "$JF_USER:$JF_PASS" -T target/news-app.war \
                      "https://trialyth1ui.jfrog.io/artifactory/${targetPath}"
                """
            }
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
