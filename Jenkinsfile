pipeline {
    agent {
        kubernetes {
            label 'wordsmith-api'
            yaml """
  spec:
    containers:
    - name: jnlp
    - name: jdk
      image: openjdk:8-jdk
      command:
      - cat
      tty: true
"""
        }
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        disableConcurrentBuilds()
    }
    stages {
        stage('Update database changes') {
            environment {
                PG_SQL_CREDS = credentials('postgresql.preview')
                PG_SQL_JDBC_URL = 'jdbc:postgresql://wordsmith-preview.ca3tifbqfpuf.us-east-1.rds.amazonaws.com:5432/wordsmith'
            }
            steps {
                container('jdk') {
                    script {
                        APPLICATION_VERSION = readFile("target/VERSION")
                    }
                    withMaven(mavenOpts: '-Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=warn') {
                        sh """
                            # Display all changes which will be applied by the Update command
                            ./mvnw liquibase:status
                            
                            # Update the database
                            ./mvnw liquibase:update
                            
                            # Create a tag in order to rollback if needed
                            ./mvnw liquibase tag:${APPLICATION_VERSION}
                        """
                    }
                }
            }
        }
    }
}