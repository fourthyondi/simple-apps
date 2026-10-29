pipeline {
    agent { label 'devops1-ebs' }

    stages {
        stage('Pull SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/fourthyondi/simple-apps.git'
            }
        }
        
        stage('Build') {
            steps {
                sh'''
                cd apps
                npm install
                '''
            }
        }

          stage('Set ENV') {
            steps {
                withCredentials ([
                    string(credentialsId:'db-name', variable: 'db-name'),
                    string(credentialsId:'db-user', variable: 'db-user'),
                    string(credentialsId:'db-password', variable: 'db-password'),
                    string(credentialsId:'db-host', variable: 'db-host'),
                    string(credentialsId:'app-port', variable: 'app-port'),
                ])
                sh'''
                cat >> .env <EOF \
                DB_NAME=${db-name} \
                DB_HOST=${db-host} \
                DB_USER=${db-user} \
                DB_PASS=${db-password} \
                APP_PORT=${app-port} \
                EOF

                '''
            }
        }

        
        stage('Testing') {
            steps {
                sh'''
                cd apps
                npm run test:coverage
                '''
            }
        }
        
        stage('Code Review') {
            steps {
                sh'''
                cd apps
                sonar-scanner \
                -Dsonar.projectKey=simple-apps \
                -Dsonar.sources=. \
                -Dsonar.host.url=http://172.23.13.87:9000 \
                -Dsonar.token=sqp_c4b30cccbc40e4fb0dd82a94baca9554c2f82ded
                '''
            }
        }
        
        stage('Deploy') {
            steps {
                sh'''
                docker compose up --build -d
                '''
            }
        }
        
        
    }
}