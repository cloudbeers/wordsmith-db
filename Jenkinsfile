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
                        MAVEN_OPTS = "--batch-mode -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=warn"
                        liquibaseChangeLogs = ["v1.0.0", "v1.1.0", "v1.2.0"]

                        sh "./mvnw $MAVEN_OPTS validate"

                        for (liquibaseChangeLog in liquibaseChangeLogs) {
                            changeLogFile = "src/main/liquibase/changelog-${liquibaseChangeLog}.xml"
                            sh """
                               # Display all changes which will be applied by the Update command
                               ./mvnw $MAVEN_OPTS liquibase:status -Dliquibase.changeLogFile=${changeLogFile}
                               
                               # Update the database
                               ./mvnw $MAVEN_OPTS liquibase:update -Dliquibase.changeLogFile=${changeLogFile}
                            """
                        } // for
                        DB_TAG_VERSION = readFile("target/VERSION")
                        sh """
                          # Create a tag in order to rollback if needed
                          ./mvnw $MAVEN_OPTS liquibase:tag -Dliquibase.tag=${DB_TAG_VERSION}

                          cat src/main/liquibase/changelog-* > target/liquibaseConcatenatedChangeLogs.txt
                        """
                        archiveArtifacts artifacts: "target/liquibaseConcatenatedChangeLogs.txt", fingerprint: true
                    }
                }
            }
        }
    }
}