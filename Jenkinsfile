pipeline {
    agent { label 'slave2' }

    environment {
        WAR_FILE = "target/news-app.war"
        ARTIFACTORY_SERVER = "jfrog"
        TOMCAT_PATH = "/opt/tomcat10/webapps"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
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
                    def currentDate = new java.text.SimpleDateFormat("yyyy-MM-dd_HH-mm").format(new Date())
                    def targetPath = "pradeep.devops.releases/${currentDate}/"

                    rtUpload(
                        serverId: "${ARTIFACTORY_SERVER}",
                        spec: """
                        {
                            "files": [
                                {
                                    "pattern": "${WAR_FILE}",
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

                    sudo rm -rf $TOMCAT_PATH/news-app $TOMCAT_PATH/news-app.war
                    sudo cp $WAR_FILE $TOMCAT_PATH/

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
