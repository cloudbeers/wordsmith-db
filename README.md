   # How-to use
   
   ## Create a new version
   Create a new Changelog file (located in _src/main/liquibase_) (ex: _src/main/liquibase/changelog-v1.0.0.xml_).
   Create a directory for the new  version : _src/main/liquibase/v2.0.0_.
   Add new Changesets in this directory and reference them in the Changelog.
   
   ## Update database
   Use Maven:
   ```
   mvn liquibase:update -Dliquibase.changeLogFile=src/main/liquibase/changelog-v2.0.0.xml
   ```
   