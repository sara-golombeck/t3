// pipeline {
//     agent any
    
//     tools {
//         maven 'Maven-3.6.2'
//         jdk 'JAVA_8'
//     }
    
//     environment {
//         DOCKER_IMAGE = 'thumbnailer'
//         DOCKER_TAG = '1.0-SNAPSHOT'
//     }
    
//     triggers {
//         githubPush()
//     }
    
//     stages {
//         stage('Install') {
//             steps {
//                 configFileProvider([configFile(fileId: 'Jenkins-Artifactory', variable: 'MAVEN_SETTINGS')]) {
//                     sh "mvn -U -s $MAVEN_SETTINGS clean install"
//                 }
//             }
//         }
        
//         stage('Test Docker Image') {
//             steps {
//                 script {
//                     sh 'docker rm -f thumbnailer || true'
                    
//                     sh """
//                         docker run --name thumbnailer \
//                             -v /home/ubuntu/examples:/pics \
//                             ${DOCKER_IMAGE}:${DOCKER_TAG} \
//                             ls /pics
//                     """
//                 }
//             }
//         }


//     }
    
//     // post {
//     //     always {
//     //         // sh 'docker rm -f thumbnailer || true'
//     //     }
//     // }
// }


pipeline {
    agent any
    
    tools {
        maven 'Maven-3.6.2'
        jdk 'JAVA_8'
    }
    
    environment {
        DOCKER_IMAGE = 'thumbnailer'
        DOCKER_TAG = '1.0-SNAPSHOT'
    }
    
    triggers {
        githubPush()
    }
    
    stages {
        stage('Install') {
            steps {
                configFileProvider([configFile(fileId: 'Jenkins-Artifactory', variable: 'MAVEN_SETTINGS')]) {
                    sh "mvn -U -s $MAVEN_SETTINGS clean install"
                }
            }
        }
        
        // stage('Test Docker Image') {
        //     steps {
        //         script {
        //             sh 'docker rm -f thumbnailer || true'
                    
        //             // יצירת קונטיינר ריק
        //             sh "docker create --name thumbnailer ${DOCKER_IMAGE}:${DOCKER_TAG} ls /pics"
                    
        //             // העתקת התיקייה examples מהפרויקט לתוך הקונטיינר
        //             sh "docker cp \${WORKSPACE}/examples/. thumbnailer:/pics/"
                    
        //             // הפעלת הקונטיינר עם הפקודה ls
        //             sh "docker start -a thumbnailer"
        //         }
        //     }
        // }
    
stage('Test Docker Image') {
    steps {
        script {
            // ניקוי קונטיינר קודם אם קיים
            sh 'docker rm -f thumbnailer || true'
            
            // יצירת קונטיינר בלי לציין פקודה, כך שיפעיל את האנטריפוינט
            sh "docker create --name thumbnailer ${DOCKER_IMAGE}:${DOCKER_TAG}"
            
            // העתקת התיקייה examples מהפרויקט לתוך הקונטיינר
            sh "docker cp \${WORKSPACE}/examples/. thumbnailer:/pics/"
            
            // הפעלת הקונטיינר - זה יריץ את האנטריפוינט אוטומטית
            sh "docker start -a thumbnailer"
            
            // העתקת התוצאות בחזרה לתיקיית הפרויקט (אופציונלי)
            sh "docker cp thumbnailer:/pics/. \${WORKSPACE}/examples/"
            
            // הצגת התוצאות העדכניות
            sh "ls -la \${WORKSPACE}/examples/"
        }
    }
}
    
    }
    
    // post {
    //     always {
    //         sh 'docker rm -f thumbnailer || true'
    //     }
    // }
}
