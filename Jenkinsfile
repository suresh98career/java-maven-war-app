pipeline{
    agent{
        label 'agent'
    }

    tools {
        maven 'Maven_3.9.7'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/suresh98career/java-maven-war-app']])
            }

        }

        stage('Maven-Build'){
            steps{
                sh 'mvn clean install'
            }
        }

         stage('Sonar Scan'){
              steps{
                  withSonarQubeEnv("Sonarqube") {
                     sh "${tool("Sonar_6.0.0")}/bin/sonar-scanner \
                       -Dsonar.host.url=http://3.108.252.226:9000/ \
                       -Dsonar.login=sqp_c9af92394e414d95c131d5c44eb7a2f18c83c0a4 \
                       -Dsonar.java.binaries=target \
                       -Dsonar.projectKey=java-maven-war-app"
                 }
             }
         }

        stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('deployment'){
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }

    }
}
