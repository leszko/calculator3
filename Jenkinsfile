pipeline { 

 
     agent any 

triggers { 

 
     pollSCM('* * * * *') 

 
} 

 
     stages { 

 
          stage("Compile") { 

 
               steps { 

 
                    sh "./gradlew compileJava" 

 
               } 

 
          } 

 
          stage("Unit test") { 

 
               steps { 

 
                    sh "./gradlew test" 

 
               } 

 
          } 
stage("Code coverage") { 

 
     steps { 

 
          sh "./gradlew jacocoTestReport" 

 
          sh "./gradlew jacocoTestCoverageVerification" 

 
     } 

 
} 


stage("Static code analysis") { 

 
     steps { 

 
          sh "./gradlew checkstyleMain" 

 
     } 

 
} 

stage("Package") { 
     steps { 
          sh "./gradlew build" 
     } 
} 
 
stage("Docker build") { 
     steps { 
          sh "docker build -t leszko/calculator:${BUILD_TIMESTAMP} ." 
     } 
} 

stage("Docker push") { 
     steps { 
          sh "docker push leszko/calculator:${BUILD_TIMESTAMP}" 
     } 
} 

stage("Update version") { 

    steps { 

        sh "sed -i 's/{{VERSION}}/${BUILD_TIMESTAMP}/g' deployment.yaml" 

    } 

} 

stage("Deploy to staging") { 
     steps { 
        sh "kubectl config use-context staging" 

        sh "kubectl apply -f hazelcast.yaml" 

        sh "kubectl apply -f deployment.yaml" 

        sh "kubectl apply -f service.yaml" 
     } 
} 

stage("Acceptance test") { 
     steps { 
          sleep 60 
          sh "chmod +x acceptance-test.sh && ./acceptance-test.sh"
     } 
} 

stage("Release") { 

    steps { 

        sh "kubectl config use-context production" 

        sh "kubectl apply -f hazelcast.yaml" 

        sh "kubectl apply -f deployment.yaml" 

        sh "kubectl apply -f service.yaml" 

    } 

} 

stage("Smoke test") { 

    steps { 

        sleep 60 

        sh "chmod +x smoke-test.sh && ./smoke-test.sh" 

    } 

} 

 
     } 

} 
