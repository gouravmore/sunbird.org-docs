---
title: Migration script to convert user email to lowercase
page_title: Migration script to convert user email to lowercase
description: Migration script to convert user email to lowercase
published: true
allowSearch: true
---

## Overview
Sunbird, from its release version 1.13 onwards, captures the email of the users in lower case. Email of the users onboarded prior to this release version might have upper case characters. To ensure data consistency across users, this migration to convert upper case characters to lower case in email has to be done.

## Prerequisites

To run the migration script, ensure you have:

1. Access to cassandra database
2. A backup of sunbird keyspace in Cassandra DB.

## Configuration Parameters
The following parameters needs to be passed as arguments for the email case-change migration job

 S.No. | Parameter | Description | Example 
-------|-----------|-------------|---------
1 | sunbird_cassandra_server | Cassandra DB IP Address| 198.168.1.1
2 | sunbird_cassandra_port | Cassandra DB Port Number | 9042 
3 | sunbird_cassandra_username* | Cassandra DB User Name | username 
4 | sunbird_cassandra_password* | Cassandra DB Password | password 
5 | sunbird_cassandra_keyspace  | Cassandra DB Keyspace Name | sunbird 
6 | sunbird_encryption_algorithm  | Encryption Algorithm to encrypt email | AES 
7 | sunbird_encryption_iteration_count  | Encryption Iteration Count to encrypt email | 3 
8 | dry_run  | if enabled, only report generation and not actual migration happens | true 

> Note: If authentication is not required, pass `""` for parameters, username, and password

## Environment Variables
The following parameters needs to be set in environment variables

 S.No. | Parameter | Description | Example 
-------|-----------|-------------|---------
1 | sunbird_encryption_key | Encryption Key to encrypt email | Encryption Key
2 | sunbird_cipher_key | Cipher Key to encrypt email | Cipher Key 

## Migration Script

1. Extract the archive file UserEmailCaseChangeMigrationBin.zip.

2. Run the script using below commands (first with dry run option for verification purpose and later without out it perform actual migration).

(dry_run: true)
``` 
sh UserEmailCaseChangeMigration_run.sh --context_param sunbird_cassandra_server="{sunbird_cassandra_server}" --context_param sunbird_cassandra_port="{sunbird_cassandra_port}" --context_param sunbird_cassandra_username="{sunbird_cassandra_username}" --context_param sunbird_cassandra_password="{sunbird_cassandra_password}" --context_param sunbird_cassandra_keyspace="{sunbird_cassandra_keyspace}" --context_param sunbird_encryption_algorithm="{sunbird_encryption_algorithm}" --context_param sunbird_encryption_iteration_count="{sunbird_encryption_iteration_count}" --context_param dry_run=true
```

Verify the records identified to be updated.

(dry_run: false)  
``` 
sh UserEmailCaseChangeMigration_run.sh --context_param sunbird_cassandra_server="{sunbird_cassandra_server}" --context_param sunbird_cassandra_port="{sunbird_cassandra_port}" --context_param sunbird_cassandra_username="{sunbird_cassandra_username}" --context_param sunbird_cassandra_password="{sunbird_cassandra_password}" --context_param sunbird_cassandra_keyspace="{sunbird_cassandra_keyspace}" --context_param sunbird_encryption_algorithm="{sunbird_encryption_algorithm}" --context_param sunbird_encryption_iteration_count="{sunbird_encryption_iteration_count}" --context_param dry_run=false
```

3. The script generates the following logs.

 S.No. | Log File | Description | Example 
-------|-----------|-------------|---------
1 | emailsWithUpperCase_timestamp.csv | Email Ids available for case-change migration | emailsWithUpperCase_1546937413175.csv
2 | toSync.csv | User Ids to be synced (This file will be generated only in migration) | toSync.csv 

4. In case of migration, Sync job has to be run for user ids captured in toSync.csv file to sync the migrated data from cassandra to elastic search.
