pipeline {
    agent any
    environment {
      PATH = "$PATH:/opt/apache-maven-3.9.4/bin"
    }
    
    stages {

        stage('CLEAN WORKSPACE') {
            steps {
                cleanWs()
            }
        }

        stage('CODE CHECKOUT') {
            steps {
                git 'https://github.com/CloudSantosh/application.git'
            }
        }

        stage('MODIFIED IMAGE TAG') {
            steps {
                sh '''
                   sed "s/image-name:latest/$JOB_NAME:v1.$BUILD_ID/g" playbooks/dep_svc.yml
                   sed -i "s/image-name:latest/$JOB_NAME:v1.$BUILD_ID/g" playbooks/dep_svc.yml
                   sed -i "s/IMAGE_NAME/$JOB_NAME:v1.$BUILD_ID/g" webapp/src/main/webapp/index.jsp                   
                   '''
            }            
        }        
      
        stage('BUILD') {
            steps {
                sh 'mvn clean install package'
            }
        } 
      
        stage('SONAR SCANNER') {
            environment {
            sonar_token = credentials('SONAR-TOKEN')
            }
            steps {
                sh 'mvn clean verify sonar:sonar -e -Dsonar.projectName=$JOB_NAME \
                    -Dsonar.projectKey=$JOB_NAME \
                    -Dsonar.host.url=http://3.145.90.33:9000 \
                    -Dsonar.login=${sonar_token}'
            }

        post {
            success {
                    echo 'SonarQube analysis completed successfully!'
            }
            failure {
                    echo 'SonarQube analysis failed. Check the SonarQube dashboard for issues.'
            }

        } 
        }
      /*         
   
    
        stage('COPY JAR & DOCKERFILE') {
            steps {
                sh 'ansible-playbook $WORKSPACE/playbooks/create_directory.yml'
            }
        }
   
        stage('PUSH IMAGE ON DOCKERHUB') {
            environment {
            dockerhub_user = credentials('DOCKERHUB_USER')            
            dockerhub_pass = credentials('DOCKERHUB_PASS')
            }    
            steps {
                sh 'ansible-playbook playbooks/push_dockerhub.yml \
                    --extra-vars "JOB_NAME=$JOB_NAME" \
                    --extra-vars "BUILD_ID=$BUILD_ID" \
                    --extra-vars "dockerhub_user=$dockerhub_user" \
                    --extra-vars "dockerhub_pass=$dockerhub_pass"'              
            }
        }
        
        stage('DEPLOYMENT ON EKS') {
            steps {
                sh 'ansible-playbook $WORKSPACE/playbooks/create_pod_on_eks.yml \
                    --extra-vars "JOB_NAME=$JOB_NAME"'
            }            
        }          
*/
    }
}      
