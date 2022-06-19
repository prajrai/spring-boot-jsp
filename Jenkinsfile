pipeline {
    agent any

    triggers { cron('* * * * *') } 
    tools {
        maven 'mvn-3.8.5'
    }
    
    parameters {
        string(name: 'SERVER_IP', defaultValue: '127.0.0.1', description: 'Provide production server IP Address.')
    }

    stages {
        stage('Source') {
            steps {
                git branch: 'main', changelog: false, credentialsId: 'c30cbeef-fb29-4b61-85fc-22dfd091a915', poll: false, url: 'https://github.com/prajrai/spring-boot-jsp.git'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Copying Artifcats') {
            environment {
                PUB_KEY = credentials( 'web-srv-pub' ) 
            }
            steps {
                sh '''
                    version=$(perl -nle 'print "$1" if /<version>(v\\d+\\.\\d+\\.\\d+)<\\/version>/' pom.xml)
                    rsync -avzPe 'ssh -i $PUB_KEY' target/news-${version}.jar root@$3.14.152.24:~/
                '''
            }
        }
    }
}
