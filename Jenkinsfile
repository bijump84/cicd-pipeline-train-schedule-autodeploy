pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "bijump84/train-schedule"
    }
    stages {
        stage("Checkout from github repo"){
            steps{
            git url: 'https://github.com/bijump84/cicd-pipeline-train-schedule-autodeploy.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') {
            steps {
                kubeconfig(caCertificate: 'MIIDBjCCAe6gAwIBAgIBATANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwptaW5pa3ViZUNBMB4XDTI0MDExMTA1NTE0M1oXDTM0MDEwOTA1NTE0M1owFTETMBEGA1UEAxMKbWluaWt1YmVDQTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN4dg8tGfI6Yg/IDuAw9/Ei0Lv9s0fNQnhel7Zr0OBfUOmxEOqw5t4lldEN54DSAYnEVQwevKneTYTj2v62T+dNxT0wGCo21kQN9lVoufzpzqbJ6qsvA/dPt5mgFV6Dj/t/EBIUK2D0QxEm74uwqt8ZB4fJasaIhbZUfeSgpw9BCDJV04oq/Bk6EueVULE9du/2vg+UCLk7M9DpORIiBXO9wuCwbsuYcpRRvwb9I1nfG6omOo2LQtnTghlGUrqmxSOP0PEqJ4+7mqASaGvHrbtpYwXiyYa6eX/x7vqecXAOsoaniXVuT4To4N8ZGVvywhUaNxlInCbFfh5XSDgPpHFMCAwEAAaNhMF8wDgYDVR0PAQH/BAQDAgKkMB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAPBgNVHRMBAf8EBTADAQH/MB0GA1UdDgQWBBSpYNdnYVZHHVaMUv2PiTKNnTPz4TANBgkqhkiG9w0BAQsFAAOCAQEAb6ZH34BMGyQvFucQah/bDwPMtTYf5Y3uxZ1nLrbtBvYKAiRx3mPlpM7luSuk954pOQIx1dsU6AUyYxTnlyl8UUr0KxPgxWpRXk2HPDpcdrDH+Xs4TkANOZBVGRqpJ8jcAHpoBOofE6OxsPFQz27aYAOl0HxiwUNUVZEYk8rBwdtXIq4m9Ql8nRjaUM5G5zBKWlIy8QEEXWZiHjprqKhrKqKt79WSTe88I9+DNYVMNkDeaOkmUXURMLlsjVM6v7+pSTk0MVlZ2a5laLxGg5aiMVBmRp3iULgXfVyvCWjjVyVKidPq77iCLuh57jZQ+mm3E+jS7ITEimygMlCeBdRG0w==', credentialsId: 'kubernetes', serverUrl: 'https://192.168.49.2:8443') {
                 sh 'kubectl apply -f deployment.yaml'
                 sh 'kubectl apply -f app-service.yaml'
                 sh 'kubectl rollout restart deployment train-schedule'
}
            }
        }
    }
}
