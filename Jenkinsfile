pipeline{
    agent any
    stages{
        stage("git clone"){
            steps{
               // git branch: 'main', url: 'https://github.com/praveenakumara/tomcat.git'
                git branch: 'main', url: 'https://github.com/praveenakumara/newmavenproject.git'
            }
        }
        stage("sonar scan"){
            steps{
                withSonarQubeEnv("sonar") {
                sh "mvn clean verify sonar:sonar -Dsonar.projectKey=sonarscan -Dsonar.projectName='sonarscan'"
                     }
                }
        }
        stage("code compile"){
            steps{
                  sh "mvn clean install"
                  sh "cp -R /var/lib/jenkins/workspace/pipeline/target/praveen.war ."   // copy the war file to the current work directory.
                  sh "mv praveen.war praveen-${BUILD_NUMBER}.war" // renaming the build number.
            }
        }
        stage("deploy to jfrog artifact"){
            steps{
                  rtUpload (
                    serverId: 'jfrog_project',  // server id is the "instance id" of jfrog system configuraton
                    spec: '''{
                        "files": [
                        {
                            "pattern": "praveen-${BUILD_NUMBER}.war",
                             "target": "praveenakumara"  
                        }
                                 ]
                    }'''    // target: <is the arttifactory repository name like "myproject","maven_project"..etc
                )
            }
        }
        stage("deploy to tomcat"){
            steps{
            deploy adapters: [tomcat9(credentialsId: '84d573af-0c82-460c-88a9-b5bdef45bdac', path: '', url: 'http://43.204.215.204:9090/')], contextPath: 'my_project', war: '**/*.war'    
            }
        }
    }
}
