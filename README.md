   # How-to use
   
   ## Create a new version
   Create a new Changelog file (located in _src/main/liquibase_) (ex: _src/main/liquibase/changelog-v1.0.0.xml_).
   Add new Changesets in the Changelog file (no sub files as changelog will be fingerprinted and archived by Jenkins).
   
   ## Update database
   Use Maven:
   ```
   mvn liquibase:update -Dliquibase.changeLogFile=src/main/liquibase/changelog-v2.0.0.xml
   ```
   