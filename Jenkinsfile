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
        //             // sh 'docker rm -f thumbnailer || true'
        //             // sh "docker create --name thumbnailer ${DOCKER_IMAGE}:${DOCKER_TAG}"
        //             // sh "docker cp \${WORKSPACE}/examples/. thumbnailer:/pics/"
        //             // sh "docker start -a thumbnailer" 
        //             sh "docker run --name thumbnailer-extended -v \${WORKSPACE}/examples:/pics ${DOCKER_IMAGE}:${DOCKER_TAG}"
        //             sh "docker rm -f thumbnailer-extended || true" 
        //         }
        //     }
        // }
        
        // stage('Test Docker Image') {
        //     steps {
        //         script {
        //             // הרצת הקונטיינר עם volume
        //             def exitCode = sh(script: "docker run --name thumbnailer-extended -v \${WORKSPACE}/examples:/pics thumbnailer:1.0-SNAPSHOT", returnStatus: true)
                    
        //             // בדיקת קוד היציאה
        //             if (exitCode != 0) {
        //                 error "Docker container failed with exit code ${exitCode}"
        //             }
                    
        //             // אופציונלי: העתקת התמונות שנוצרו לתיקיית artifacts
        //             sh "mkdir -p \${WORKSPACE}/thumbnails"
        //             sh "docker cp thumbnailer-extended:/pics/thumbnails/. \${WORKSPACE}/thumbnails/ || true"
        //             sh "docker cp thumbnailer-extended:/pics/thumbnails/. \${WORKSPACE}/thumbnails/ || true"
        //             sh "docker exec -it thumbnailer-extended bash"
        //             sh ""
                    
        //             // אופציונלי: רשימת הקבצים שנוצרו
        //             sh "ls -la \${WORKSPACE}/thumbnails"
                    
        //             // ניקוי
        //             sh "docker rm -f thumbnailer-extended || true"
        //         }
        //     }
        
        stage('Test Docker Image') {
            steps {
                script {
                    // הרצת הקונטיינר
                    // sh "docker run -d --name thumbnailer-extended -v \${WORKSPACE}/examples:/pics thumbnailer:1.0-SNAPSHOT"
                    sh "docker run -d --name thumbnailer-extended -v \${WORKSPACE}/examples:/pics thumbnailer:1.0-SNAPSHOT tail -f /dev/null"
                    // בדיקת תוכן תיקיית /pics בקונטיינר
                    sh "docker exec thumbnailer-extended ls -la /pics || echo 'Cannot list /pics directory'"
                    
                    // בדיקה האם קיימת תיקיית thumbnails כלשהי
                    sh "docker exec thumbnailer-extended find / -name 'thumbnails' -type d || echo 'No thumbnails directory found'"
                    sh "docker logs thumbnailer-extended"
                    // בדיקה היכן נשמרות התמונות המעובדות (בהנחה שיש קבצי תמונה חדשים)
                    sh "docker exec thumbnailer-extended find / -name '*.jpg' -o -name '*.png' -o -name '*.jpeg' -mmin -5 || echo 'No recently modified images found'"
                    
                    // יצירת תיקיית thumbnails במארח
                    sh "mkdir -p \${WORKSPACE}/thumbnails"
                    
                    // ניסיון העתקה
                    sh "docker cp thumbnailer-extended:/pics/. \${WORKSPACE}/thumbnails/ || echo 'Failed to copy files from /pics/'"
                    
                    // הצגת תוכן התיקייה במארח לאחר ההעתקה
                    sh "ls -la \${WORKSPACE}/thumbnails"
                    
                    // ניקוי
                    sh "docker rm -f thumbnailer-extended || true"
                }
            }
            post {
                success {
                    // שמירת התמונות כ-artifacts של ג'נקינס
                    archiveArtifacts artifacts: 'thumbnails/**', allowEmptyArchive: true
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