---
description: Guidelines for liquibase Spring Boot setup
---

# Liquibase

### Folder structure

```
resources
    liquibase
        changelogs
            v1_initial_setup.xml
            v2_users_table_rename.xml
            v3_1.1_release.xml
        master.xml
```

#### master.xml (example)

It can include all change-logs files by referencing path

```xml
<?xml version="1.0" encoding="UTF-8"?>   
<databaseChangeLog
   xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xmlns:pro="http://www.liquibase.org/xml/ns/pro"
   xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
      http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.1.xsd
      http://www.liquibase.org/xml/ns/pro 
      http://www.liquibase.org/xml/ns/pro/liquibase-pro-4.1.xsd">  
    <includeAll path="com/example/db/changelog/"/>  
</databaseChangeLog> 
```

#### Change log file (v1\_initial\_setup.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>   
<databaseChangeLog
   xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xmlns:pro="http://www.liquibase.org/xml/ns/pro"
   xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
      http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.1.xsd
      http://www.liquibase.org/xml/ns/pro 
      http://www.liquibase.org/xml/ns/pro/liquibase-pro-4.1.xsd">
   <changeSet  author="nvoxland"  id="v1_1">
      <createTable tableName="person">
         <column  name="id"  type="INTEGER">
            <constraints  nullable="false"  primaryKey="true"  unique="true"/>
         </column>
         <column  name="name"  type="VARCHAR(255)" />
      </createTable>
   </changeSet>
   <changeSet  author="nvoxland"  id="v1_2">
      ..
   </changeSet>
   <changeSet  author="nvoxland"  id="v1_3">
      ..
   </changeSet>
</databaseChangeLog>
```

Change sets should be commented.&#x20;

We strongly encourage having only **one change per changeset**. This makes each change atomic within a single transaction. Each changeset either succeeds or fails. If it fails, it can be corrected and redeployed until it succeeds. Multiple independent changes in one changeset create a risk that some changes deploy while a later change fails. This leaves the database in a partially deployed state which requires manual intervention to correct.

The exception is when you have several changes you want to be grouped as a single transaction – in that case, multiple statements in the changeset if the correct choice.
