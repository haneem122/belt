pipeline { 
 
    agent any
 
    stages { 
 
        stage('Build'){ 
            steps { 
                sh "mvn clean" 
 
                sh "mvn compile" 
            } 
        } 
 pipeline { 
    agent any 
 
    environment { 
 
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id') 
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key') 
 
        AWS_S3_BUCKET = "haneen-jenkins" 
        ARTIFACT_NAME = "hello-world.war" 
        AWS_EB_APP_NAME = "jenkenis" 
        AWS_EB_APP_VERSION = "${BUILD_ID}" 
        AWS_EB_ENVIRONMENT = "Jenkenis-env" 
 
    } 
 
    stages { 
        stage('Validate') { 
            steps { 
                 
                sh "mvn validate" 
 
                sh "mvn clean" 
 
            } 
        } 
 
         stage('Build') { 
            steps { 
                 
                sh "mvn compile" 
 
            } 
        } 
 
        stage('Test') { 
            steps { 
                 
                sh "mvn test" 
 
            } 
 
            post { 
                always { 
                    junit '**/target/surefire-reports/TEST-*.xml' 
                } 
            } 
        } 
 
        stage('Package') { 
            steps { 
                 
                sh "mvn package" 
 
            } 
 
            post { 
                success { 
                    archiveArtifacts artifacts: '**/target/**.war', followSymlinks: false 
 
                    
                } 
            } 
        } 
 
        stage('Publish artefacts to S3 Bucket') { 
            steps { 
 
                sh "aws configure set region us-east-1" 
 
                sh "aws s3 cp ./target/**.war s3://$AWS_S3_BUCKET/$ARTIFACT_NAME" 
                 
            } 
        } 
 
        stage('Deploy') { 
            steps { 
 
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME' 
 
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION' 
             
                 
            } 
        } 
         
    } 
}

        stage('Test'){ 
            steps { 
 
                sh "mvn test" 
            } 
 
            post { 
                always { 
                    junit '**/target/surefire-reports/TEST-*.xml' 
                } 
            } 
        } 
 
  
 
        stage('Package'){ 
            steps { 
 
                sh "mvn package" 
            } 
            post { 
                success { 
                    archiveArtifacts artifacts: '**/target/*.war', followSymlinks: false 
                } 
            } 
        } 
 
    } 
 
}
pipeline { 
 
    agent { 
        docker { 
            image "maven:3.8.6-jdk-11" 
        } 
    } 
 
    stages { 
 
        stage('Build'){ 
            steps { 
                sh "mvn clean" 
 
                sh "mvn compile" 
            } 
        } 
 
        stage('Test'){ 
            steps { 
 
                sh "mvn test" 
            } 
 
            post { 
                always { 
                    junit '**/target/surefire-reports/TEST-*.xml' 
                } 
            } 
        } 
 
  
 
        stage('Package'){ 
            steps { 
 
                sh "mvn package" 
            } 
            post { 
                success { 
                    archiveArtifacts artifacts: '**/target/*.war', followSymlinks: false 
                } 
            } 
        } 
 
    } 
 
}
