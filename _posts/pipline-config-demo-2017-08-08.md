---
layout: post
title: jenkins pipline config demo
category: jenkins
tags: [devops,jenkins,pipline]
---

# jenkins pipline config simple-demo

```
pipeline { 
    agent any  
    environment { 
        JAVA_HOME="D:/Java/jdk1.8.0_131"
    }

    stages {
        stage('Build') { 
            steps { 
               echo 'start build war.' 
               
               dir('D:/ProjectBuild'){
                    
                    bat "mvn install -Dmaven.test.skip=true"
                    script {
                        wfile=readFile "D:/ProjectBuild/target/warname.txt"
                    }
               }
            }
        }
        stage('Deploy') { 
            steps { 
                echo "start deploy war with ${wfile}"
                dir("D:/ProjectBuild"){
                bat "mvn install -f pom-deploy-v1.xml -Dwarfile=${wfile}"
            
                }
            }
        }
        stage ('Publish') {
            steps{
              build job: 'update_config', parameters: [
                [$class: 'StringParameterValue', name: 'isPublish', value: "Y"]
              ]
            }
        }
    }
}
```
