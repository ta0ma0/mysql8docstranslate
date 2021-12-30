MySQL :: MySQL 8.0 Reference Manual :: 1.3 What Is New in MySQL 8.0

[MySQL 8.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/)  /  [General Information](https://dev.mysql.com/doc/refman/8.0/en/introduction.html)  /  What Is New in MySQL 8.0

## <a id="mysql-nutshell"></a>1.3 What Is New in MySQL 8.0

This section summarizes what has been added to, deprecated in, and removed from MySQL 8.0. A companion section lists MySQL server options and variables that have been added, deprecated, or removed in MySQL 8.0; see [Section 1.4, “Server and Status Variables and Options Added, Deprecated, or Removed in MySQL 8.0”](https://dev.mysql.com/doc/refman/8.0/en/added-deprecated-removed.html "1.4 Server and Status Variables and Options Added, Deprecated, or Removed in MySQL 8.0").

- [Features Added in MySQL 8.0](https://dev.mysql.com/doc/refman/8.0/en/mysql-nutshell.html#mysql-nutshell-additions "Features Added in MySQL 8.0")
    
- [Features Deprecated in MySQL 8.0](https://dev.mysql.com/doc/refman/8.0/en/mysql-nutshell.html#mysql-nutshell-deprecations "Features Deprecated in MySQL 8.0")
    
- [Features Removed in MySQL 8.0](https://dev.mysql.com/doc/refman/8.0/en/mysql-nutshell.html#mysql-nutshell-removals "Features Removed in MySQL 8.0")
    

### <a id="mysql-nutshell-additions"></a>Features Added in MySQL 8.0

<a id="idm45429587686832"></a><a id="idm45429587685824"></a>

The following features have been added to MySQL 8.0:

- **Data dictionary. ** MySQL now incorporates a transactional data dictionary that stores information about database objects. In previous MySQL releases, dictionary data was stored in metadata files and nontransactional tables. For more information, see [Chapter 14, *MySQL Data Dictionary*](https://dev.mysql.com/doc/refman/8.0/en/data-dictionary.html "Chapter 14 MySQL Data Dictionary").
    
    <a id="idm45429587681040"></a><a id="idm45429587679648"></a>
- **Atomic data definition statements (Atomic DDL). ** An atomic DDL statement combines the data dictionary updates, storage engine operations, and binary log writes associated with a DDL operation into a single, atomic transaction. For more information, see [Section 13.1.1, “Atomic Data Definition Statement Support”](https://dev.mysql.com/doc/refman/8.0/en/atomic-ddl.html "13.1.1 Atomic Data Definition Statement Support").
    
    <a id="idm45429587675936"></a><a id="idm45429587674544"></a>
- **Upgrade procedure. ** Previously, after installation of a new version of MySQL, the MySQL server automatically upgrades the data dictionary tables at the next startup, after which the DBA is expected to invoke [**mysql_upgrade**](https://dev.mysql.com/doc/refman/8.0/en/mysql-upgrade.html "4.4.5 mysql_upgrade — Check and Upgrade MySQL Tables") manually to upgrade the system tables in the `mysql` schema, as well as objects in other schemas such as the `sys` schema and user schemas.
    
    <a id="idm45429587668944"></a><a id="idm45429587667552"></a>
    
    As of MySQL 8.0.16, the server performs the tasks previously handled by [**mysql_upgrade**](https://dev.mysql.com/doc/refman/8.0/en/mysql-upgrade.html "4.4.5 mysql_upgrade — Check and Upgrade MySQL Tables"). After installation of a new MySQL version, the server now automatically performs all necessary upgrade tasks at the next startup and is not dependent on the DBA invoking [**mysql_upgrade**](https://dev.mysql.com/doc/refman/8.0/en/mysql-upgrade.html "4.4.5 mysql_upgrade — Check and Upgrade MySQL Tables"). In addition, the server updates the contents of the help tables (something [**mysql_upgrade**](https://dev.mysql.com/doc/refman/8.0/en/mysql-upgrade.html "4.4.5 mysql_upgrade — Check and Upgrade MySQL Tables") did not do). A new [`--upgrade`](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_upgrade) server option provides control over how the server performs automatic data dictionary and server upgrade operations. For more information, see [Section 2.11.3, “What the MySQL Upgrade Process Upgrades”](https://dev.mysql.com/doc/refman/8.0/en/upgrading-what-is-upgraded.html "2.11.3 What the MySQL Upgrade Process Upgrades").
    
- **Security and account management. ** These enhancements were added to improve security and enable greater DBA flexibility in account management:
    
    <a id="idm45429587659184"></a><a id="idm45429587657792"></a>
    
    - The grant tables in the `mysql` system database are now `InnoDB` (transactional) tables. Previously, these were `MyISAM` (nontransactional) tables. The change of grant table storage engine underlies an accompanying change to the behavior of account-management statements. Previously, an account-management statement (such as [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html "13.7.1.3 CREATE USER Statement") or [`DROP USER`](https://dev.mysql.com/doc/refman/8.0/en/drop-user.html "13.7.1.5 DROP USER Statement")) that named multiple users could succeed for some users and fail for others. Now, each statement is transactional and either succeeds for all named users or rolls back and has no effect if any error occurs. The statement is written to the binary log if it succeeds, but not if it fails; in that case, rollback occurs and no changes are made. For more information, see [Section 13.1.1, “Atomic Data Definition Statement Support”](https://dev.mysql.com/doc/refman/8.0/en/atomic-ddl.html "13.1.1 Atomic Data Definition Statement Support").
        
    - A new `caching_sha2_password` authentication plugin is available. Like the `sha256_password` plugin, `caching_sha2_password` implements SHA-256 password hashing, but uses caching to address latency issues at connect time. It also supports more transport protocols and does not require linking against OpenSSL for RSA key pair-based password-exchange capabilities. See [Section 6.4.1.2, “Caching SHA-2 Pluggable Authentication”](https://dev.mysql.com/doc/refman/8.0/en/caching-sha2-pluggable-authentication.html "6.4.1.2 Caching SHA-2 Pluggable Authentication").
        
        The `caching_sha2_password` and `sha256_password` authentication plugins provide more secure password encryption than the `mysql_native_password` plugin, and `caching_sha2_password` provides better performance than `sha256_password`. Due to these superior security and performance characteristics of `caching_sha2_password`, it is now the preferred authentication plugin, and is also the default authentication plugin rather than `mysql_native_password`. For information about the implications of this change of default plugin for server operation and compatibility of the server with clients and connectors, see [caching\_sha2\_password as the Preferred Authentication Plugin](https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html#upgrade-caching-sha2-password "caching_sha2_password as the Preferred Authentication Plugin").
        
    - The MySQL Enterprise Edition SASL LDAP authentication plugin now supports GSSAPI/Kerberos as an authentication method for MySQL clients and servers on Linux. This is useful in Linux environments where applications access LDAP using Microsoft Active Directory, which has Kerberos enabled by default. See [LDAP Authentication Methods](https://dev.mysql.com/doc/refman/8.0/en/ldap-pluggable-authentication.html#ldap-pluggable-authentication-auth-methods "LDAP Authentication Methods").
        
    - MySQL Enterprise Edition now supports an authentication method that enables users to authenticate to MySQL Server using Kerberos, provided that appropriate Kerberos tickets are available or can be obtained. For details, see [Section 6.4.1.8, “Kerberos Pluggable Authentication”](https://dev.mysql.com/doc/refman/8.0/en/kerberos-pluggable-authentication.html "6.4.1.8 Kerberos Pluggable Authentication").
        
    - MySQL now supports roles, which are named collections of privileges. Roles can be created and dropped. Roles can have privileges granted to and revoked from them. Roles can be granted to and revoked from user accounts. The active applicable roles for an account can be selected from among those granted to the account, and can be changed during sessions for that account. For more information, see [Section 6.2.10, “Using Roles”](https://dev.mysql.com/doc/refman/8.0/en/roles.html "6.2.10 Using Roles").
        
    - MySQL now incorporates the concept of user account categories, with system and regular users distinguished according to whether they have the [`SYSTEM_USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_system-user) privilege. See [Section 6.2.11, “Account Categories”](https://dev.mysql.com/doc/refman/8.0/en/account-categories.html "6.2.11 Account Categories").
        
    - Previously, it was not possible to grant privileges that apply globally except for certain schemas. This is now possible if the [`partial_revokes`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_partial_revokes) system variable is enabled. See [Section 6.2.12, “Privilege Restriction Using Partial Revokes”](https://dev.mysql.com/doc/refman/8.0/en/partial-revokes.html "6.2.12 Privilege Restriction Using Partial Revokes").
        
    - The [`GRANT`](https://dev.mysql.com/doc/refman/8.0/en/grant.html "13.7.1.6 GRANT Statement") statement has an ``AS `user` [WITH ROLE]`` clause that specifies additional information about the privilege context to use for statement execution. This syntax is visible at the SQL level, although its primary purpose is to enable uniform replication across all nodes of grantor privilege restrictions imposed by partial revokes, by causing those restrictions to appear in the binary log. See [Section 13.7.1.6, “GRANT Statement”](https://dev.mysql.com/doc/refman/8.0/en/grant.html "13.7.1.6 GRANT Statement").
        
    - MySQL now maintains information about password history, enabling restrictions on reuse of previous passwords. DBAs can require that new passwords not be selected from previous passwords for some number of password changes or period of time. It is possible to establish password-reuse policy globally as well as on a per-account basis.
        
        It is now possible to require that attempts to change account passwords be verified by specifying the current password to be replaced. This enables DBAs to prevent users from changing password without proving that they know the current password. It is possible to establish password-verification policy globally as well as on a per-account basis.
        
        Accounts are now permitted to have dual passwords, which enables phased password changes to be performed seamlessly in complex multiple-server systems, without downtime.
        
        MySQL now enables administrators to configure user accounts such that too many consecutive login failures due to incorrect passwords cause temporary account locking. The required number of failures and the lock time are configurable per account.
        
        These new capabilities provide DBAs more complete control over password management. For more information, see [Section 6.2.15, “Password Management”](https://dev.mysql.com/doc/refman/8.0/en/password-management.html "6.2.15 Password Management").
        
    - MySQL now supports FIPS mode, if compiled using OpenSSL, and an OpenSSL library and FIPS Object Module are available at runtime. FIPS mode imposes conditions on cryptographic operations such as restrictions on acceptable encryption algorithms or requirements for longer key lengths. See [Section 6.8, “FIPS Support”](https://dev.mysql.com/doc/refman/8.0/en/fips-mode.html "6.8 FIPS Support").
        
    - The TLS context the server uses for new connections now is reconfigurable at runtime. This capability may be useful, for example, to avoid restarting a MySQL server that has been running so long that its SSL certificate has expired. See [Server-Side Runtime Configuration and Monitoring for Encrypted Connections](https://dev.mysql.com/doc/refman/8.0/en/using-encrypted-connections.html#using-encrypted-connections-server-side-runtime-configuration "Server-Side Runtime Configuration and Monitoring for Encrypted Connections").
        
    - OpenSSL 1.1.1 supports the TLS v1.3 protocol for encrypted connections, and MySQL 8.0.16 and higher supports TLS v1.3 as well, if both the server and client are compiled using OpenSSL 1.1.1 or higher. See [Section 6.3.2, “Encrypted Connection TLS Protocols and Ciphers”](https://dev.mysql.com/doc/refman/8.0/en/encrypted-connection-protocols-ciphers.html "6.3.2 Encrypted Connection TLS Protocols and Ciphers").
        
    - MySQL now sets the access control granted to clients on the named pipe to the minimum necessary for successful communication on Windows. Newer MySQL client software can open named pipe connections without any additional configuration. If older client software cannot be upgraded immediately, the new [`named_pipe_full_access_group`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_named_pipe_full_access_group) system variable can be used to give a Windows group the necessary permissions to open a named pipe connection. Membership in the full-access group should be restricted and temporary.
        
    - Previously, MySQL user accounts authenticated to the server using a single authentication method. As of MySQL 8.0.27, MySQL supports multifactor authentication (MFA), which makes it possible to create accounts that have up to three authentication methods. MFA support entails these changes:
        
        - [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html "13.7.1.3 CREATE USER Statement") and [`ALTER USER`](https://dev.mysql.com/doc/refman/8.0/en/alter-user.html "13.7.1.1 ALTER USER Statement") syntax has been extended to permit specification of multiple authentication methods.
            
        - The [`authentication_policy`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_authentication_policy) system variable enables MFA policy to be established by controlling how many factors can be used and the types of authentication permitted for each factor. This places constraints on how the authentication-related clauses of [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html "13.7.1.3 CREATE USER Statement") and [`ALTER USER`](https://dev.mysql.com/doc/refman/8.0/en/alter-user.html "13.7.1.1 ALTER USER Statement") statements may be used.
            
        - Client programs have new [`--password1`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_password1), [`--password2`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_password2), and [`--password3`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_password3) command-line options for specifying multiple passwords. For applications that use the C API, the new `MYSQL_OPT_USER_PASSWORD` option for the [`mysql_options4()`](https://dev.mysql.com/doc/c-api/8.0/en/mysql-options4.html) C API function enables the same capability.
            
        
        In addition, MySQL Enterprise Edition now supports authentication to MySQL Server using devices such as smart cards, security keys, and biometric readers. This authentication method is based on the Fast Identity Online (FIDO) standard, and uses a pair of plugins, `authentication_fido` on the server side and `authentication_fido_client` on the client side. The server-side FIDO authentication plugin is included only in MySQL Enterprise Edition distributions. It is not included in MySQL community distributions. However, the client-side plugin is included in all distributions, including community distributions. This enables clients from any distribution to connect to a server that has the server-side plugin loaded.
        
        Multifactor authentication can use existing MySQL authentication methods, the new FIDO authentication method, or a combination of both. For more information, see [Section 6.2.18, “Multifactor Authentication”](https://dev.mysql.com/doc/refman/8.0/en/multifactor-authentication.html "6.2.18 Multifactor Authentication"), and [Section 6.4.1.11, “FIDO Pluggable Authentication”](https://dev.mysql.com/doc/refman/8.0/en/fido-pluggable-authentication.html "6.4.1.11 FIDO Pluggable Authentication").
        
    
- **Resource management. ** MySQL now supports creation and management of resource groups, and permits assigning threads running within the server to particular groups so that threads execute according to the resources available to the group. Group attributes enable control over its resources, to enable or restrict resource consumption by threads in the group. DBAs can modify these attributes as appropriate for different workloads. Currently, CPU time is a manageable resource, represented by the concept of “virtual CPU” as a term that includes CPU cores, hyperthreads, hardware threads, and so forth. The server determines at startup how many virtual CPUs are available, and database administrators with appropriate privileges can associate these CPUs with resource groups and assign threads to groups. For more information, see [Section 5.1.16, “Resource Groups”](https://dev.mysql.com/doc/refman/8.0/en/resource-groups.html "5.1.16 Resource Groups").
    
    <a id="idm45429587587520"></a><a id="idm45429587586128"></a>
- **Table encryption management. ** Table encryption can now be managed globally by defining and enforcing encryption defaults. The [`default_table_encryption`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_table_encryption) variable defines an encryption default for newly created schemas and general tablespace. The encryption default for a schema can also be defined using the `DEFAULT ENCRYPTION` clause when creating a schema. By default, a table inherits the encryption of the schema or general tablespace it is created in. Encryption defaults are enforced by enabling the [`table_encryption_privilege_check`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_table_encryption_privilege_check) variable. The privilege check occurs when creating or altering a schema or general tablespace with an encryption setting that differs from the [`default_table_encryption`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_table_encryption) setting, or when creating or altering a table with an encryption setting that differs from the default schema encryption. The [`TABLE_ENCRYPTION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_table-encryption-admin) privilege permits overriding default encryption settings when [`table_encryption_privilege_check`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_table_encryption_privilege_check) is enabled. For more information, see [Defining an Encryption Default for Schemas and General Tablespaces](https://dev.mysql.com/doc/refman/8.0/en/innodb-data-encryption.html#innodb-schema-tablespace-encryption-default "Defining an Encryption Default for Schemas and General Tablespaces").
    
    <a id="idm45429587575872"></a><a id="idm45429587574480"></a>
- **InnoDB enhancements. ** These `InnoDB` enhancements were added:
    
    <a id="idm45429587570928"></a><a id="idm45429587569536"></a>
    
    - The current maximum auto-increment counter value is written to the redo log each time the value changes, and saved to an engine-private system table on each checkpoint. These changes make the current maximum auto-increment counter value persistent across server restarts. Additionally:
        
        - A server restart no longer cancels the effect of the `AUTO_INCREMENT = N` table option. If you initialize the auto-increment counter to a specific value, or if you alter the auto-increment counter value to a larger value, the new value is persisted across server restarts.
            
        - A server restart immediately following a [`ROLLBACK`](https://dev.mysql.com/doc/refman/8.0/en/commit.html "13.3.1 START TRANSACTION, COMMIT, and ROLLBACK Statements") operation no longer results in the reuse of auto-increment values that were allocated to the rolled-back transaction.
            
        - If you modify an `AUTO_INCREMENT` column value to a value larger than the current maximum auto-increment value (in an [`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/update.html "13.2.13 UPDATE Statement") operation, for example), the new value is persisted, and subsequent [`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/insert.html "13.2.6 INSERT Statement") operations allocate auto-increment values starting from the new, larger value.
            
        
        For more information, see [Section 15.6.1.6, “AUTO_INCREMENT Handling in InnoDB”](https://dev.mysql.com/doc/refman/8.0/en/innodb-auto-increment-handling.html "15.6.1.6 AUTO_INCREMENT Handling in InnoDB"), and [InnoDB AUTO_INCREMENT Counter Initialization](https://dev.mysql.com/doc/refman/8.0/en/innodb-auto-increment-handling.html#innodb-auto-increment-initialization "InnoDB AUTO_INCREMENT Counter Initialization").
        
    - When encountering index tree corruption, `InnoDB` writes a corruption flag to the redo log, which makes the corruption flag crash safe. `InnoDB` also writes in-memory corruption flag data to an engine-private system table on each checkpoint. During recovery, `InnoDB` reads corruption flags from both locations and merges results before marking in-memory table and index objects as corrupt.
        
    - The `InnoDB` **memcached** plugin supports multiple `get` operations (fetching multiple key-value pairs in a single **memcached** query) and range queries. See [Section 15.20.4, “InnoDB memcached Multiple get and Range Query Support”](https://dev.mysql.com/doc/refman/8.0/en/innodb-memcached-multiple-get-range-query.html "15.20.4 InnoDB memcached Multiple get and Range Query Support").
        
    - A new dynamic variable, [`innodb_deadlock_detect`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_deadlock_detect), may be used to disable deadlock detection. On high concurrency systems, deadlock detection can cause a slowdown when numerous threads wait for the same lock. At times, it may be more efficient to disable deadlock detection and rely on the [`innodb_lock_wait_timeout`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_lock_wait_timeout) setting for transaction rollback when a deadlock occurs.
        
        <a id="idm45429587547232"></a><a id="idm45429587545840"></a>
    - The new [`INFORMATION_SCHEMA.INNODB_CACHED_INDEXES`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-cached-indexes-table.html "26.4.5 The INFORMATION_SCHEMA INNODB_CACHED_INDEXES Table") table reports the number of index pages cached in the `InnoDB` buffer pool for each index.
        
    - `InnoDB` temporary tables are now created in the shared temporary tablespace, `ibtmp1`.
        
    - The `InnoDB` [tablespace encryption feature](https://dev.mysql.com/doc/refman/8.0/en/innodb-data-encryption.html "15.13 InnoDB Data-at-Rest Encryption") supports encryption of redo log and undo log data. See [Redo Log Encryption](https://dev.mysql.com/doc/refman/8.0/en/innodb-data-encryption.html#innodb-data-encryption-redo-log "Redo Log Encryption"), and [Undo Log Encryption](https://dev.mysql.com/doc/refman/8.0/en/innodb-data-encryption.html#innodb-data-encryption-undo-log "Undo Log Encryption").
        
    - `InnoDB` supports `NOWAIT` and `SKIP LOCKED` options with `SELECT ... FOR SHARE` and `SELECT ... FOR UPDATE` locking read statements. `NOWAIT` causes the statement to return immediately if a requested row is locked by another transaction. `SKIP LOCKED` removes locked rows from the result set. See [Locking Read Concurrency with NOWAIT and SKIP LOCKED](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-reads.html#innodb-locking-reads-nowait-skip-locked "Locking Read Concurrency with NOWAIT and SKIP LOCKED").
        
        `SELECT ... FOR SHARE` replaces `SELECT ... LOCK IN SHARE MODE`, but `LOCK IN SHARE MODE` remains available for backward compatibility. The statements are equivalent. However, `FOR UPDATE` and `FOR SHARE` support `NOWAIT`, `SKIP LOCKED`, and ``OF `tbl_name` `` options. See [Section 13.2.10, “SELECT Statement”](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement").
        
        ``OF `tbl_name` `` applies locking queries to named tables.
        
    - `ADD PARTITION`, `DROP PARTITION`, `COALESCE PARTITION`, `REORGANIZE PARTITION`, and `REBUILD PARTITION` [`ALTER TABLE`](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html "13.1.9 ALTER TABLE Statement") options are supported by native partitioning in-place APIs and may be used with `ALGORITHM={COPY|INPLACE}` and `LOCK` clauses.
        
        `DROP PARTITION` with `ALGORITHM=INPLACE` deletes data stored in the partition and drops the partition. However, `DROP PARTITION` with `ALGORITHM=COPY` or [`old_alter_table=ON`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_old_alter_table) rebuilds the partitioned table and attempts to move data from the dropped partition to another partition with a compatible `PARTITION ... VALUES` definition. Data that cannot be moved to another partition is deleted.
        
    - The `InnoDB` storage engine now uses the MySQL data dictionary rather than its own storage engine-specific data dictionary. For information about the data dictionary, see [Chapter 14, *MySQL Data Dictionary*](https://dev.mysql.com/doc/refman/8.0/en/data-dictionary.html "Chapter 14 MySQL Data Dictionary").
        
    - `mysql` system tables and data dictionary tables are now created in a single `InnoDB` tablespace file named `mysql.ibd` in the MySQL data directory. Previously, these tables were created in individual `InnoDB` tablespace files in the `mysql` database directory.
        
    - The following undo tablespace changes are introduced in MySQL 8.0:
        
        - By default, undo logs now reside in two undo tablespaces that are created when the MySQL instance is initialized. Undo logs are no longer created in the system tablespace.
            
        - As of MySQL 8.0.14, additional undo tablespaces can be created in a chosen location at runtime using [`CREATE UNDO TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/create-tablespace.html "13.1.21 CREATE TABLESPACE Statement") syntax.
            
            ```sql
            CREATE UNDO TABLESPACE tablespace_name ADD DATAFILE 'file_name.ibu';
            ```
            
            Undo tablespaces created using [`CREATE UNDO TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/create-tablespace.html "13.1.21 CREATE TABLESPACE Statement") syntax can be dropped at runtime using [`DROP UNDO TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/drop-tablespace.html "13.1.33 DROP TABLESPACE Statement") syntax.
            
            ```sql
            DROP UNDO TABLESPACE tablespace_name;
            ```
            
            [`ALTER UNDO TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/alter-tablespace.html "13.1.10 ALTER TABLESPACE Statement") syntax can be used to mark an undo tablespace as active or inactive.
            
            ```sql
            ALTER UNDO TABLESPACE tablespace_name SET {ACTIVE|INACTIVE};
            ```
            
            A `STATE` column that shows the state of a tablespace was added to the [`INFORMATION_SCHEMA.INNODB_TABLESPACES`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-tablespaces-table.html "26.4.24 The INFORMATION_SCHEMA INNODB_TABLESPACES Table") table. An undo tablespace must be in an `empty` state before it can be dropped.
            
        - The [`innodb_undo_log_truncate`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_undo_log_truncate) variable is enabled by default.
            
        - The [`innodb_rollback_segments`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_rollback_segments) variable defines the number of rollback segments per undo tablespace. Previously, [`innodb_rollback_segments`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_rollback_segments) specified the total number of rollback segments for the MySQL instance. This change increases the number of rollback segments available for concurrent transactions. More rollback segments increases the likelihood that concurrent transactions use separate rollback segments for undo logs, resulting in less resource contention.
            
        
    - Default values for variables that affect buffer pool preflushing and flushing behavior were modified:
        
        - The [`innodb_max_dirty_pages_pct_lwm`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_max_dirty_pages_pct_lwm) default value is now 10. The previous default value of 0 disables buffer pool preflushing. A value of 10 enables preflushing when the percentage of dirty pages in the buffer pool exceeds 10%. Enabling preflushing improves performance consistency.
            
        - The [`innodb_max_dirty_pages_pct`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_max_dirty_pages_pct) default value was increased from 75 to 90. `InnoDB` attempts to flush data from the buffer pool so that the percentage of dirty pages does not exceed this value. The increased default value permits a greater percentage of dirty pages in the buffer pool.
            
        
    - The default [`innodb_autoinc_lock_mode`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_autoinc_lock_mode) setting is now 2 (interleaved). Interleaved lock mode permits the execution of multi-row inserts in parallel, which improves concurrency and scalability. The new [`innodb_autoinc_lock_mode`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_autoinc_lock_mode) default setting reflects the change from statement-based replication to row based replication as the default replication type in MySQL 5.7. Statement-based replication requires the consecutive auto-increment lock mode (the previous default) to ensure that auto-increment values are assigned in a predictable and repeatable order for a given sequence of SQL statements, whereas row-based replication is not sensitive to the execution order of SQL statements. For more information, see [InnoDB AUTO_INCREMENT Lock Modes](https://dev.mysql.com/doc/refman/8.0/en/innodb-auto-increment-handling.html#innodb-auto-increment-lock-modes "InnoDB AUTO_INCREMENT Lock Modes").
        
        For systems that use statement-based replication, the new [`innodb_autoinc_lock_mode`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_autoinc_lock_mode) default setting may break applications that depend on sequential auto-increment values. To restore the previous default, set [`innodb_autoinc_lock_mode`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_autoinc_lock_mode) to 1.
        
    - Renaming a general tablespace is supported by [`ALTER TABLESPACE ... RENAME TO`](https://dev.mysql.com/doc/refman/8.0/en/alter-tablespace.html "13.1.10 ALTER TABLESPACE Statement") syntax.
        
    - The new [`innodb_dedicated_server`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_dedicated_server) variable, which is disabled by default, can be used to have `InnoDB` automatically configure the following options according to the amount of memory detected on the server:
        
        - [`innodb_buffer_pool_size`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)
            
        - [`innodb_log_file_size`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_file_size)
            
        - [`innodb_flush_method`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_flush_method)
            
        
        This option is intended for MySQL server instances that run on a dedicated server. For more information, see [Section 15.8.12, “Enabling Automatic Configuration for a Dedicated MySQL Server”](https://dev.mysql.com/doc/refman/8.0/en/innodb-dedicated-server.html "15.8.12 Enabling Automatic Configuration for a Dedicated MySQL Server").
        
    - The new [`INFORMATION_SCHEMA.INNODB_TABLESPACES_BRIEF`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-tablespaces-brief-table.html "26.4.25 The INFORMATION_SCHEMA INNODB_TABLESPACES_BRIEF Table") view provides space, name, path, flag, and space type data for `InnoDB` tablespaces.
        
    - The [zlib library](http://www.zlib.net/) version bundled with MySQL was raised from version 1.2.3 to version 1.2.11. MySQL implements compression with the help of the zlib library.
        
        If you use `InnoDB` compressed tables, see [Section 2.11.4, “Changes in MySQL 8.0”](https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html "2.11.4 Changes in MySQL 8.0") for related upgrade implications.
        
    - Serialized dictionary information (SDI) is present in all `InnoDB` tablespace files except for global temporary tablespace and undo tablespace files. SDI is serialized metadata for table and tablespace objects. The presence of SDI data provides metadata redundancy. For example, dictionary object metadata may be extracted from tablespace files if the data dictionary becomes unavailable. SDI extraction is performed using the [**ibd2sdi**](https://dev.mysql.com/doc/refman/8.0/en/ibd2sdi.html "4.6.1 ibd2sdi — InnoDB Tablespace SDI Extraction Utility") tool. SDI data is stored in `JSON` format.
        
        The inclusion of SDI data in tablespace files increases tablespace file size. An SDI record requires a single index page, which is 16KB in size by default. However, SDI data is compressed when it is stored to reduce the storage footprint.
        
    - The `InnoDB` storage engine now supports atomic DDL, which ensures that DDL operations are either fully committed or rolled back, even if the server halts during the operation. For more information, see [Section 13.1.1, “Atomic Data Definition Statement Support”](https://dev.mysql.com/doc/refman/8.0/en/atomic-ddl.html "13.1.1 Atomic Data Definition Statement Support").
        
    - Tablespace files can be moved or restored to a new location while the server is offline using the [`innodb_directories`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_directories) option. For more information, see [Section 15.6.3.6, “Moving Tablespace Files While the Server is Offline”](https://dev.mysql.com/doc/refman/8.0/en/innodb-moving-data-files-offline.html "15.6.3.6 Moving Tablespace Files While the Server is Offline").
        
    - The following redo logging optimizations were implemented:
        
        - User threads can now write concurrently to the log buffer without synchronizing writes.
            
        - User threads can now add dirty pages to the flush list in a relaxed order.
            
        - A dedicated log thread is now responsible for writing the log buffer to the system buffers, flushing system buffers to disk, notifying user threads about written and flushed redo, maintaining the lag required for the relaxed flush list order, and write checkpoints.
            
        - System variables were added for configuring the use of spin delay by user threads waiting for flushed redo:
            
            - [`innodb_log_wait_for_flush_spin_hwm`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_wait_for_flush_spin_hwm): Defines the maximum average log flush time beyond which user threads no longer spin while waiting for flushed redo.
                
            - [`innodb_log_spin_cpu_abs_lwm`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_spin_cpu_abs_lwm): Defines the minimum amount of CPU usage below which user threads no longer spin while waiting for flushed redo.
                
            - [`innodb_log_spin_cpu_pct_hwm`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_spin_cpu_pct_hwm): Defines the maximum amount of CPU usage above which user threads no longer spin while waiting for flushed redo.
                
            
        - The [`innodb_log_buffer_size`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_buffer_size) variable is now dynamic, which permits resizing of the log buffer while the server is running.
            
        
        For more information, see [Section 8.5.4, “Optimizing InnoDB Redo Logging”](https://dev.mysql.com/doc/refman/8.0/en/optimizing-innodb-logging.html "8.5.4 Optimizing InnoDB Redo Logging").
        
    - As of MySQL 8.0.12, undo logging is supported for small updates to large object (LOB) data, which improves performance of LOB updates that are 100 bytes in size or less. Previously, LOB updates were a minimum of one LOB page in size, which is less than optimal for updates that might only modify a few bytes. This enhancement builds upon support added in MySQL 8.0.4 for partial update of LOB data.
        
    - As of MySQL 8.0.12, `ALGORITHM=INSTANT` is supported for the following [`ALTER TABLE`](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html "13.1.9 ALTER TABLE Statement") operations:
        
        - Adding a column. This feature is also referred to as “Instant `ADD COLUMN`”. Limitations apply. See [Section 15.12.1, “Online DDL Operations”](https://dev.mysql.com/doc/refman/8.0/en/innodb-online-ddl-operations.html "15.12.1 Online DDL Operations").
            
        - Adding or dropping a virtual column.
            
        - Adding or dropping a column default value.
            
        - Modifying the definition of an [`ENUM`](https://dev.mysql.com/doc/refman/8.0/en/enum.html "11.3.5 The ENUM Type") or [`SET`](https://dev.mysql.com/doc/refman/8.0/en/set.html "11.3.6 The SET Type") column.
            
        - Changing the index type.
            
        - Renaming a table.
            
        
        Operations that support `ALGORITHM=INSTANT` only modify metadata in the data dictionary. No metadata locks are taken on the table, and table data is unaffected, making the operations instantaneous. If not specified explicitly, `ALGORITHM=INSTANT` is used by default by operations that support it. If `ALGORITHM=INSTANT` is specified but not supported, the operation fails immediately with an error.
        
        For more information about operations that support `ALGORITHM=INSTANT`, see [Section 15.12.1, “Online DDL Operations”](https://dev.mysql.com/doc/refman/8.0/en/innodb-online-ddl-operations.html "15.12.1 Online DDL Operations").
        
    - As of MySQL 8.0.13, the `TempTable` storage engine supports storage of binary large object (BLOB) type columns. This enhancement improves performance for queries that use temporary tables containing BLOB data. Previously, temporary tables that contained BLOB data were stored in the on-disk storage engine defined by [`internal_tmp_disk_storage_engine`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_internal_tmp_disk_storage_engine). For more information, see [Section 8.4.4, “Internal Temporary Table Use in MySQL”](https://dev.mysql.com/doc/refman/8.0/en/internal-temporary-tables.html "8.4.4 Internal Temporary Table Use in MySQL").
        
    - As of MySQL 8.0.13, the `InnoDB` data-at-rest encryption feature supports general tablespaces. Previously, only file-per-table tablespaces could be encrypted. To support encryption of general tablespaces, [`CREATE TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/create-tablespace.html "13.1.21 CREATE TABLESPACE Statement") and [`ALTER TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/alter-tablespace.html "13.1.10 ALTER TABLESPACE Statement") syntax was extended to include an `ENCRYPTION` clause.
        
        The [`INFORMATION_SCHEMA.INNODB_TABLESPACES`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-tablespaces-table.html "26.4.24 The INFORMATION_SCHEMA INNODB_TABLESPACES Table") table now includes an `ENCRYPTION` column that indicates whether or not a tablespace is encrypted.
        
        The `stage/innodb/alter tablespace (encryption)` Performance Schema stage instrument was added to permit monitoring of general tablespace encryption operations.
        
    - Disabling the `innodb_buffer_pool_in_core_file` variable reduces the size of core files by excluding `InnoDB` buffer pool pages. To use this variable, the [`core_file`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_core_file) variable must be enabled and the operating system must support the `MADV_DONTDUMP` non-POSIX extension to `madvise()`, which is supported in Linux 3.4 and later. For more information, see [Section 15.8.3.7, “Excluding Buffer Pool Pages from Core Files”](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool-in-core-file.html "15.8.3.7 Excluding Buffer Pool Pages from Core Files").
        
    - As of MySQL 8.0.13, user-created temporary tables and internal temporary tables created by the optimizer are stored in session temporary tablespaces that are allocated to a session from a pool of temporary tablespaces. When a session disconnects, its temporary tablespaces are truncated and released back to the pool. In previous releases, temporary tables were created in the global temporary tablespace (`ibtmp1`), which did not return disk space to the operating system after temporary tables were dropped.
        
        The [`innodb_temp_tablespaces_dir`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_temp_tablespaces_dir) variable defines the location where session temporary tablespaces are created. The default location is the `#innodb_temp` directory in the data directory.
        
        The [`INNODB_SESSION_TEMP_TABLESPACES`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-session-temp-tablespaces-table.html "26.4.22 The INFORMATION_SCHEMA INNODB_SESSION_TEMP_TABLESPACES Table") table provides metadata about session temporary tablespaces.
        
        The global temporary tablespace (`ibtmp1`) now stores rollback segments for changes made to user-created temporary tables.
        
    - As of MySQL 8.0.14, `InnoDB` supports parallel clustered index reads, which can improve [`CHECK TABLE`](https://dev.mysql.com/doc/refman/8.0/en/check-table.html "13.7.3.2 CHECK TABLE Statement") performance. This feature does not apply to secondary index scans. The [`innodb_parallel_read_threads`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_parallel_read_threads) session variable must be set to a value greater than 1 for parallel clustered index reads to occur. The default value is 4. The actual number of threads used to perform a parallel clustered index read is determined by the [`innodb_parallel_read_threads`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_parallel_read_threads) setting or the number of index subtrees to scan, whichever is smaller.
        
    - As of 8.0.14, when the [`innodb_dedicated_server`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_dedicated_server) variable is enabled, the size and number of log files are configured according to the automatically configured buffer pool size. Previously, log file size was configured according to the amount of memory detected on the server, and the number of log files was not configured automatically.
        
    - As of 8.0.14, the `ADD DATAFILE` clause of the [`CREATE TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/create-tablespace.html "13.1.21 CREATE TABLESPACE Statement") statement is optional, which permits users without the [`FILE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_file) privilege to create tablespaces. A [`CREATE TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/create-tablespace.html "13.1.21 CREATE TABLESPACE Statement") statement executed without an `ADD DATAFILE` clause implicitly creates a tablespace data file with a unique file name.
        
    - By default, when the amount of memory occupied by the TempTable storage engine exceeds the memory limit defined by the [`temptable_max_ram`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_temptable_max_ram) variable, the TempTable storage engine begins allocating memory-mapped temporary files from disk. As of MySQL 8.0.16, this behavior is controlled by the [`temptable_use_mmap`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_temptable_use_mmap) variable. Disabling [`temptable_use_mmap`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_temptable_use_mmap) causes the TempTable storage engine to use `InnoDB` on-disk internal temporary tables instead of memory-mapped files as its overflow mechanism. For more information, see [Internal Temporary Table Storage Engine](https://dev.mysql.com/doc/refman/8.0/en/internal-temporary-tables.html#internal-temporary-tables-engines "Internal Temporary Table Storage Engine").
        
    - As of MySQL 8.0.16, the `InnoDB` data-at-rest encryption feature supports encryption of the `mysql` system tablespace. The `mysql` system tablespace contains the `mysql` system database and the MySQL data dictionary tables. For more information, see [Section 15.13, “InnoDB Data-at-Rest Encryption”](https://dev.mysql.com/doc/refman/8.0/en/innodb-data-encryption.html "15.13 InnoDB Data-at-Rest Encryption").
        
    - The [`innodb_spin_wait_pause_multiplier`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_spin_wait_pause_multiplier) variable, introduced in MySQL 8.0.16, provides greater control over the duration of spin-lock polling delays that occur when a thread waits to acquire a mutex or rw-lock. Delays can be tuned more finely to account for differences in PAUSE instruction duration on different processor architectures. For more information, see [Section 15.8.8, “Configuring Spin Lock Polling”](https://dev.mysql.com/doc/refman/8.0/en/innodb-performance-spin_lock_polling.html "15.8.8 Configuring Spin Lock Polling").
        
    - `InnoDB` parallel read thread performance for large data sets was improved in MySQL 8.0.17 through better utilization of read threads, through a reduction in read thread I/O for prefetch activity that occurs during parallel scans, and through support for parallel scanning of partitions.
        
        The parallel read thread feature is controlled by the [`innodb_parallel_read_threads`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_parallel_read_threads) variable. The maximum setting is now 256, which is the total number of threads for all client connections. If the thread limit is reached, connections fall back to using a single thread.
        
    - The [`innodb_idle_flush_pct`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_idle_flush_pct) variable, introduced in MySQL 8.0.18, permits placing a limit on page flushing during idle periods, which can help extend the life of solid state storage devices. See [Limiting Buffer Flushing During Idle Periods](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool-flushing.html#innodb-limit-flushing-rate "Limiting Buffer Flushing During Idle Periods").
        
    - Efficient sampling of `InnoDB` data for the purpose of generating histogram statistics is supported as of MySQL 8.0.19. See [Histogram Statistics Analysis](https://dev.mysql.com/doc/refman/8.0/en/analyze-table.html#analyze-table-histogram-statistics-analysis "Histogram Statistics Analysis").
        
    - As of MySQL 8.0.20, the doublewrite buffer storage area resides in doublewrite files. In previous releases, the storage area resided in the system tablespace. Moving the storage area out of the system tablespace reduces write latency, increases throughput, and provides flexibility with respect to placement of doublewrite buffer pages. The following system variables were introduced for advanced doublewrite buffer configuration:
        
        - [`innodb_doublewrite_dir`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_doublewrite_dir)
            
            Defines the doublewrite buffer file directory.
            
        - [`innodb_doublewrite_files`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_doublewrite_files)
            
            Defines the number of doublewrite files.
            
        - [`innodb_doublewrite_pages`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_doublewrite_pages)
            
            Defines the maximum number of doublewrite pages per thread for a batch write.
            
        - [`innodb_doublewrite_batch_size`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_doublewrite_batch_size)
            
            Defines the number of doublewrite pages to write in a batch.
            
        
        For more information, see [Section 15.6.4, “Doublewrite Buffer”](https://dev.mysql.com/doc/refman/8.0/en/innodb-doublewrite-buffer.html "15.6.4 Doublewrite Buffer").
        
    - The Contention-Aware Transaction Scheduling (CATS) algorithm, which prioritizes transactions that are waiting for locks, was improved in MySQL 8.0.20. Transaction scheduling weight computation is now performed a separate thread entirely, which improves computation performance and accuracy.
        
        The First In First Out (FIFO) algorithm, which had also been used for transaction scheduling, was removed. The FIFO algorithm was rendered redundant by CATS algorithm enhancements. Transaction scheduling previously performed by the FIFO algorithm is now performed by the CATS algorithm.
        
        A `TRX_SCHEDULE_WEIGHT` column was added to the `INFORMATION_SCHEMA.INNODB_TRX` table, which permits querying transaction scheduling weights assigned by the CATS algorithm.
        
        The following `INNODB_METRICS` counters were added for monitoring code-level transaction scheduling events:
        
        - `lock_rec_release_attempts`
            
            The number of attempts to release record locks.
            
        - `lock_rec_grant_attempts`
            
            The number of attempts to grant record locks.
            
        - `lock_schedule_refreshes`
            
            The number of times the wait-for graph was analyzed to update transaction schedule weights.
            
        
        For more information, see [Section 15.7.6, “Transaction Scheduling”](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-scheduling.html "15.7.6 Transaction Scheduling").
        
    - As of MySQL 8.0.21, to improve concurrency for operations that require access to lock queues for table and row resources, the lock system mutex (`lock_sys->mutex`) was replaced in by sharded latches, and lock queues were grouped into table and page *lock queue shards*, with each shard protected by a dedicated mutex. Previously, the single lock system mutex protected all lock queues, which was a point of contention on high-concurrency systems. The new sharded implementation permits more granular access to lock queues.
        
        The lock system mutex (`lock_sys->mutex`) was replaced by the following sharded latches:
        
        - A global latch (`lock_sys->latches.global_latch`) consisting of 64 read-write lock objects (`rw_lock_t`). Access to an individual lock queue requires a shared global latch and a latch on the lock queue shard. Operations that require access to all lock queues take an exclusive global latch, which latches all table and page lock queue shards.
            
        - Table shard latches (`lock_sys->latches.table_shards.mutexes`), implemented as an array of 512 mutexes, with each mutex dedicated to one of 512 table lock queue shards.
            
        - Page shard latches (`lock_sys->latches.page_shards.mutexes`), implemented as an array of 512 mutexes, with each mutex dedicated to one of 512 page lock queue shards.
            
        
        The Performance Schema `wait/synch/mutex/innodb/lock_mutex` instrument for monitoring the single lock system mutex was replaced by instruments for monitoring the new global, table shard, and page shard latches:
        
        - `wait/synch/sxlock/innodb/lock_sys_global_rw_lock`
            
        - `wait/synch/mutex/innodb/lock_sys_table_mutex`
            
        - `wait/synch/mutex/innodb/lock_sys_page_mutex`
            
        
    - As of MySQL 8.0.21, table and table partition data files created outside of the data directory using the `DATA DIRECTORY` clause are restricted to directories known to `InnoDB`. This change permits database administrators to control where tablespace data files are created and ensures that the data files can be found during recovery.
        
        General and file-per-table tablespaces data files (`.ibd` files) can no longer be created in the undo tablespace directory ([`innodb_undo_directory`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_undo_directory)) unless that directly is known to `InnoDB`.
        
        Known directories are those defined by the [`datadir`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_datadir), [`innodb_data_home_dir`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_data_home_dir), and [`innodb_directories`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_directories) variables.
        
        Truncating an `InnoDB` table that resides in a file-per-table tablespace drops the existing tablespace and creates a new one. As of MySQL 8.0.21, `InnoDB` creates the new tablespace in the default location and writes a warning to the error log if the current tablespace directory is unknown. To have [`TRUNCATE TABLE`](https://dev.mysql.com/doc/refman/8.0/en/truncate-table.html "13.1.37 TRUNCATE TABLE Statement") create the tablespace in its current location, add the directory to the [`innodb_directories`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_directories) setting before running [`TRUNCATE TABLE`](https://dev.mysql.com/doc/refman/8.0/en/truncate-table.html "13.1.37 TRUNCATE TABLE Statement").
        
    - As of MySQL 8.0.21, redo logging can be enabled and disabled using [`ALTER INSTANCE {ENABLE|DISABLE} INNODB REDO_LOG`](https://dev.mysql.com/doc/refman/8.0/en/alter-instance.html "13.1.5 ALTER INSTANCE Statement") syntax. This functionality is intended for loading data into a new MySQL instance. Disabling redo logging helps speed up data loading by avoiding redo log writes.
        
        The new [`INNODB_REDO_LOG_ENABLE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_innodb-redo-log-enable) privilege permits enabling and disabling redo logging.
        
        The new [`Innodb_redo_log_enabled`](https://dev.mysql.com/doc/refman/8.0/en/server-status-variables.html#statvar_Innodb_redo_log_enabled) status variable permits monitoring redo logging status.
        
        See [Disabling Redo Logging](https://dev.mysql.com/doc/refman/8.0/en/innodb-redo-log.html#innodb-disable-redo-logging "Disabling Redo Logging").
        
    - At startup, `InnoDB` validates the paths of known tablespace files against tablespace file paths stored in the data dictionary in case tablespace files have been moved to a different location. The new [`innodb_validate_tablespace_paths`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_validate_tablespace_paths) variable, introduced in MySQL 8.0.21, permits disabling tablespace path validation. This feature is intended for environments where tablespaces files are not moved. Disabling tablespace path validation improves startup time on systems with a large number of tablespace files.
        
        For more information, see [Section 15.6.3.7, “Disabling Tablespace Path Validation”](https://dev.mysql.com/doc/refman/8.0/en/innodb-disabling-tablespace-path-validation.html "15.6.3.7 Disabling Tablespace Path Validation").
        
    - As of MySQL 8.0.21, on storage engines that support atomic DDL, the [`CREATE TABLE ... SELECT`](https://dev.mysql.com/doc/refman/8.0/en/create-table-select.html "13.1.20.4 CREATE TABLE ... SELECT Statement") statement is logged as one transaction in the binary log when row-based replication is in use. Previously, it was logged as two transactions, one to create the table, and the other to insert data. With this change, [`CREATE TABLE ... SELECT`](https://dev.mysql.com/doc/refman/8.0/en/create-table-select.html "13.1.20.4 CREATE TABLE ... SELECT Statement") statements are now safe for row-based replication and permitted for use with GTID-based replication. For more information, see [Section 13.1.1, “Atomic Data Definition Statement Support”](https://dev.mysql.com/doc/refman/8.0/en/atomic-ddl.html "13.1.1 Atomic Data Definition Statement Support").
        
    - Truncating an undo tablespace on a busy system could affect performance due to associated flushing operations that remove old undo tablespace pages from the buffer pool and flush the initial pages of the new undo tablespace to disk. To address this issue, the flushing operations are removed as of MySQL 8.0.21.
        
        Old undo tablespace pages are released passively as they become least recently used, or are removed at the next full checkpoint. The initial pages of the new undo tablespace are now redo logged instead of flushed to disk during the truncate operation, which also improves durability of the undo tablespace truncate operation.
        
        To prevent potential issues caused by an excessive number of undo tablespace truncate operations, truncate operations on the same undo tablespace between checkpoints are now limited to 64. If the limit is exceeded, an undo tablespace can still be made inactive, but it is not truncated until after the next checkpoint.
        
        [`INNODB_METRICS`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-metrics-table.html "26.4.21 The INFORMATION_SCHEMA INNODB_METRICS Table") counters associated with defunct undo truncate flushing operations were removed. Removed counters include: `undo_truncate_sweep_count`, `undo_truncate_sweep_usec`, `undo_truncate_flush_count`, and `undo_truncate_flush_usec`.
        
        See [Section 15.6.3.4, “Undo Tablespaces”](https://dev.mysql.com/doc/refman/8.0/en/innodb-undo-tablespaces.html "15.6.3.4 Undo Tablespaces").
        
    - As of MySQL 8.0.22, the new [`innodb_extend_and_initialize`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_extend_and_initialize) variable permits configuring how `InnoDB` allocates space to file-per-table and general tablespaces on Linux. By default, when an operation requires additional space in a tablespace, `InnoDB` allocates pages to the tablespace and physically writes NULLs to those pages. This behavior affects performance if new pages are allocated frequently. You can disable [`innodb_extend_and_initialize`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_extend_and_initialize) on Linux systems to avoid physically writing NULLs to newly allocated tablespace pages. When [`innodb_extend_and_initialize`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_extend_and_initialize) is disabled, space is allocated using `posix_fallocate()` calls, which reserve space without physically writing NULLs.
        
        A `posix_fallocate()` operation is not atomic, which makes it possible for a failure to occur between allocating space to a tablespace file and updating the file metadata. Such a failure can leave newly allocated pages in an uninitialized state, resulting in a failure when `InnoDB` attempts to access those pages. To prevent this scenario, `InnoDB` writes a redo log record before allocating a new tablespace page. If a page allocation operation is interrupted, the operation is replayed from the redo log record during recovery.
        
    - As of MySQL 8.0.23, `InnoDB` supports encryption of doublewrite file pages belonging to encrypted tablespaces. The pages are encrypted using the encryption key of the associated tablespace. For more information, see [Section 15.13, “InnoDB Data-at-Rest Encryption”](https://dev.mysql.com/doc/refman/8.0/en/innodb-data-encryption.html "15.13 InnoDB Data-at-Rest Encryption").
        
    - The [`temptable_max_mmap`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_temptable_max_mmap) variable, introduced in MySQL 8.0.23, defines the maximum amount of memory the TempTable storage engine is permitted to allocate from memory-mapped (MMAP) files before it starts storing internal temporary table data on disk. A setting of 0 disables allocation from MMAP files. For more information, see [Section 8.4.4, “Internal Temporary Table Use in MySQL”](https://dev.mysql.com/doc/refman/8.0/en/internal-temporary-tables.html "8.4.4 Internal Temporary Table Use in MySQL").
        
    - The `AUTOEXTEND_SIZE` option, introduced in MySQL 8.0.23, defines the amount by which `InnoDB` extends the size of a tablespace when it becomes full, making it possible to extend tablespace size in larger increments. The `AUTOEXTEND_SIZE` option is supported with the [`CREATE TABLE`](https://dev.mysql.com/doc/refman/8.0/en/create-table.html "13.1.20 CREATE TABLE Statement"), [`ALTER TABLE`](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html "13.1.9 ALTER TABLE Statement"), [`CREATE TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/create-tablespace.html "13.1.21 CREATE TABLESPACE Statement"), and [`ALTER TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/alter-tablespace.html "13.1.10 ALTER TABLESPACE Statement") statements. For more information, see [Section 15.6.3.9, “Tablespace AUTOEXTEND_SIZE Configuration”](https://dev.mysql.com/doc/refman/8.0/en/innodb-tablespace-autoextend-size.html "15.6.3.9 Tablespace AUTOEXTEND_SIZE Configuration").
        
        An `AUTOEXTEND_SIZE` size column was added to the [`INFORMATION_SCHEMA.INNODB_TABLESPACES`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-tablespaces-table.html "26.4.24 The INFORMATION_SCHEMA INNODB_TABLESPACES Table") table.
        
    - The [`innodb_segment_reserve_factor`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_segment_reserve_factor) system variable, introduced in MySQL 8.0.26, permits configuring the percentage of tablespace file segment pages that are reserved as empty pages. For more information, see [Configuring the Percentage of Reserved File Segment Pages](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-space.html#innodb-config-reserved-file-segment-pages "Configuring the Percentage of Reserved File Segment Pages").
        
    - On platforms that support `fdatasync()` system calls, the [`innodb_use_fdatasync`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_use_fdatasync) variable, introduced in MySQL 8.0.26, permits using `fdatasync()` instead of `fsync()` for operating system flushes. An `fdatasync()` system call does not flush changes to file metadata unless required for subsequent data retrieval, providing a potential performance benefit.
        
    - As of MySQL 8.0.28, the [`tmp_table_size`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_tmp_table_size) variable defines the maximum size of any individual in-memory internal temporary table created by the TempTable storage engine. An appropriate size limit prevents individual queries from consuming an inordinate amount global TempTable resources. See [Internal Temporary Table Storage Engine](https://dev.mysql.com/doc/refman/8.0/en/internal-temporary-tables.html#internal-temporary-tables-engines "Internal Temporary Table Storage Engine").
        
    
- **Character set support. ** The default character set has changed from `latin1` to `utf8mb4`. The `utf8mb4` character set has several new collations, including `utf8mb4_ja_0900_as_cs`, the first Japanese language-specific collation available for Unicode in MySQL. For more information, see [Section 10.10.1, “Unicode Character Sets”](https://dev.mysql.com/doc/refman/8.0/en/charset-unicode-sets.html "10.10.1 Unicode Character Sets").
    
    <a id="idm45429587247520"></a><a id="idm45429587246128"></a>
- **JSON enhancements. ** The following enhancements or additions were made to MySQL's JSON functionality:
    
    <a id="idm45429587243056"></a><a id="idm45429587241664"></a>
    
    - Added the [`->>`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#operator_json-inline-path) (inline path) operator, which is equivalent to calling [`JSON_UNQUOTE()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-unquote) on the result of [`JSON_EXTRACT()`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#function_json-extract).
        
        This is a refinement of the column path operator [`->`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#operator_json-column-path) introduced in MySQL 5.7; `col->>"$.path"` is equivalent to `JSON_UNQUOTE(col->"$.path")`. The inline path operator can be used wherever you can use `JSON_UNQUOTE(JSON_EXTRACT())`, such [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") column lists, `WHERE` and `HAVING` clauses, and `ORDER BY` and `GROUP BY` clauses. For more information, see the description of the operator, as well as [JSON Path Syntax](https://dev.mysql.com/doc/refman/8.0/en/json.html#json-path-syntax "JSON Path Syntax").
        
    - Added two JSON aggregation functions [`JSON_ARRAYAGG()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_json-arrayagg) and [`JSON_OBJECTAGG()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_json-objectagg). `JSON_ARRAYAGG()` takes a column or expression as its argument, and aggregates the result as a single [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") array. The expression can evaluate to any MySQL data type; this does not have to be a `JSON` value. `JSON_OBJECTAGG()` takes two columns or expressions which it interprets as a key and a value; it returns the result as a single `JSON` object. For more information and examples, see [Section 12.20, “Aggregate Functions”](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions-and-modifiers.html "12.20 Aggregate Functions").
        
    - Added the JSON utility function [`JSON_PRETTY()`](https://dev.mysql.com/doc/refman/8.0/en/json-utility-functions.html#function_json-pretty), which outputs an existing [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") value in an easy-to-read format; each JSON object member or array value is printed on a separate line, and a child object or array is intended 2 spaces with respect to its parent.
        
        This function also works with a string that can be parsed as a JSON value.
        
        For more detailed information and examples, see [Section 12.18.8, “JSON Utility Functions”](https://dev.mysql.com/doc/refman/8.0/en/json-utility-functions.html "12.18.8 JSON Utility Functions").
        
    - When sorting [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") values in a query using `ORDER BY`, each value is now represented by a variable-length part of the sort key, rather than a part of a fixed 1K in size. In many cases this can reduce excessive usage. For example, a scalar `INT` or even `BIGINT` value actually requires very few bytes, so that the remainder of this space (up to 90% or more) was taken up by padding. This change has the following benefits for performance:
        
        - Sort buffer space is now used more effectively, so that filesorts need not flush to disk as early or often as with fixed-length sort keys. This means that more data can be sorted in memory, avoiding unnecessary disk access.
            
        - Shorter keys can be compared more quickly than longer ones, providing a noticeable improvement in performance. This is true for sorts performed entirely in memory as well as for sorts that require writing to and reading from disk.
            
        
    - Added support in MySQL 8.0.2 for partial, in-place updates of `JSON` column values, which is more efficient than completely removing an existing JSON value and writing a new one in its place, as was done previously when updating any `JSON` column. For this optimization to be applied, the update must be applied using [`JSON_SET()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-set), [`JSON_REPLACE()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-replace), or [`JSON_REMOVE()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-remove). New elements cannot be added to the JSON document being updated; values within the document cannot take more space than they did before the update. See [Partial Updates of JSON Values](https://dev.mysql.com/doc/refman/8.0/en/json.html#json-partial-updates "Partial Updates of JSON Values"), for a detailed discussion of the requirements.
        
        Partial updates of JSON documents can be written to the binary log, taking up less space than logging complete JSON documents. Partial updates are always logged as such when statement-based replication is in use. For this to work with row-based replication, you must first set [`binlog_row_value_options=PARTIAL_JSON`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_binlog_row_value_options); see this variable's description for more information.
        
    - Added the JSON utility functions [`JSON_STORAGE_SIZE()`](https://dev.mysql.com/doc/refman/8.0/en/json-utility-functions.html#function_json-storage-size) and [`JSON_STORAGE_FREE()`](https://dev.mysql.com/doc/refman/8.0/en/json-utility-functions.html#function_json-storage-free). `JSON_STORAGE_SIZE()` returns the storage space in bytes used for the binary representation of a JSON document prior to any partial update (see previous item). `JSON_STORAGE_FREE()` shows the amount of space remaining in a table column of type [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") after it has been partially updated using `JSON_SET()` or `JSON_REPLACE()`; this is greater than zero if the binary representation of the new value is less than that of the previous value.
        
        Each of these functions also accepts a valid string representation of a JSON document. For such a value, `JSON_STORAGE_SIZE()` returns the space used by its binary representation following its conversion to a JSON document. For a variable containing the string representation of a JSON document, `JSON_STORAGE_FREE()` returns zero. Either function produces an error if its (non-null) argument cannot be parsed as a valid JSON document, and `NULL` if the argument is `NULL`.
        
        For more information and examples, see [Section 12.18.8, “JSON Utility Functions”](https://dev.mysql.com/doc/refman/8.0/en/json-utility-functions.html "12.18.8 JSON Utility Functions").
        
        `JSON_STORAGE_SIZE()` and `JSON_STORAGE_FREE()` were implemented in MySQL 8.0.2.
        
    - Added support in MySQL 8.0.2 for ranges such as `$[1 to 5]` in XPath expressions. Also added support in this version for the `last` keyword and relative addressing, such that `$[last]` always selects the last (highest-numbered) element in the array and `$[last-1]` the next to last element. `last` and expressions using it can also be included in range definitions. For example, `$[last-2 to last-1]` returns the last two elements but one from an array. See [Searching and Modifying JSON Values](https://dev.mysql.com/doc/refman/8.0/en/json.html#json-paths "Searching and Modifying JSON Values"), for additional information and examples.
        
    - Added a JSON merge function intended to conform to [RFC 7396](https://tools.ietf.org/html/rfc7396). [`JSON_MERGE_PATCH()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-merge-patch), when used on 2 JSON objects, merges them into a single JSON object that has as members a union of the following sets:
        
        - Each member of the first object for which there is no member with the same key in the second object.
            
        - Each member of the second object for which there is no member having the same key in the first object, and whose value is not the JSON `null` literal.
            
        - Each member having a key that exists in both objects, and whose value in the second object is not the JSON `null` literal.
            
        
        As part of this work, the [`JSON_MERGE()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-merge) function has been renamed [`JSON_MERGE_PRESERVE()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-merge-preserve). `JSON_MERGE()` continues to be recognized as an alias for `JSON_MERGE_PRESERVE()` in MySQL 8.0, but is now deprecated and is subject to removal in a future version of MySQL.
        
        For more information and examples, see [Section 12.18.4, “Functions That Modify JSON Values”](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html "12.18.4 Functions That Modify JSON Values").
        
    - Implemented “last duplicate key wins” normalization of duplicate keys, consistent with [RFC 7159](https://tools.ietf.org/html/rfc7159) and most JavaScript parsers. An example of this behavior is shown here, where only the rightmost member having the key `x` is preserved:
        
        ```sql
        mysql> SELECT JSON_OBJECT('x', '32', 'y', '[true, false]',
             >                     'x', '"abc"', 'x', '100') AS Result;
        +------------------------------------+
        | Result                             |
        +------------------------------------+
        | {"x": "100", "y": "[true, false]"} |
        +------------------------------------+
        1 row in set (0.00 sec)
        ```
        
        Values inserted into MySQL [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") columns are also normalized in this way, as shown in this example:
        
        ```sql
        mysql> CREATE TABLE t1 (c1 JSON);
        
        mysql> INSERT INTO t1 VALUES ('{"x": 17, "x": "red", "x": [3, 5, 7]}');
        
        mysql> SELECT c1 FROM t1;
        +------------------+
        | c1               |
        +------------------+
        | {"x": [3, 5, 7]} |
        +------------------+
        ```
        
        This is an incompatible change from previous versions of MySQL, where a “first duplicate key wins” algorithm was used in such cases.
        
        See [Normalization, Merging, and Autowrapping of JSON Values](https://dev.mysql.com/doc/refman/8.0/en/json.html#json-normalization "Normalization, Merging, and Autowrapping of JSON Values"), for more information and examples.
        
    - Added the [`JSON_TABLE()`](https://dev.mysql.com/doc/refman/8.0/en/json-table-functions.html#function_json-table) function in MySQL 8.0.4. This function accepts JSON data and returns it as a relational table having the specified columns.
        
        This function has the syntax ``JSON_TABLE(`expr`, `path` COLUMNS `column_list`) [AS] `alias`)``, where *`expr`* is an expression that returns JSON data, *`path`* is a JSON path applied to the source, and *`column_list`* is a list of column definitions. An example is shown here:
        
        ```sql
        mysql> SELECT *
         -> FROM
         ->   JSON_TABLE(
         ->     '[{"a":3,"b":"0"},{"a":"3","b":"1"},{"a":2,"b":1},{"a":0},{"b":[1,2]}]',
         ->     "$[*]" COLUMNS(
         ->       rowid FOR ORDINALITY,
         ->
         ->       xa INT EXISTS PATH "$.a",
         ->       xb INT EXISTS PATH "$.b",
         ->
         ->       sa VARCHAR(100) PATH "$.a",
         ->       sb VARCHAR(100) PATH "$.b",
         ->
         ->       ja JSON PATH "$.a",
         ->       jb JSON PATH "$.b"
         ->     )
         ->   ) AS  jt1;
        +-------+------+------+------+------+------+--------+
        | rowid | xa   | xb   | sa   | sb   | ja   | jb     |
        +-------+------+------+------+------+------+--------+
        |     1 |    1 |    1 | 3    | 0    | 3    | "0"    |
        |     2 |    1 |    1 | 3    | 1    | "3"  | "1"    |
        |     3 |    1 |    1 | 2    | 1    | 2    | 1      |
        |     4 |    1 |    0 | 0    | NULL | 0    | NULL   |
        |     5 |    0 |    1 | NULL | NULL | NULL | [1, 2] |
        +-------+------+------+------+------+------+--------+
        ```
        
        The JSON source expression can be any expression that yields a valid JSON document, including a JSON literal, a table column, or a function call that returns JSON such as [`JSON_EXTRACT(t1, data, '$.post.comments')`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#function_json-extract). For more information, see [Section 12.18.6, “JSON Table Functions”](https://dev.mysql.com/doc/refman/8.0/en/json-table-functions.html "12.18.6 JSON Table Functions").
        
    
- **Data type support. ** MySQL now supports use of expressions as default values in data type specifications. This includes the use of expressions as default values for the [`BLOB`](https://dev.mysql.com/doc/refman/8.0/en/blob.html "11.3.4 The BLOB and TEXT Types"), [`TEXT`](https://dev.mysql.com/doc/refman/8.0/en/blob.html "11.3.4 The BLOB and TEXT Types"), `GEOMETRY`, and [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") data types, which previously could not be assigned default values at all. For details, see [Section 11.6, “Data Type Default Values”](https://dev.mysql.com/doc/refman/8.0/en/data-type-defaults.html "11.6 Data Type Default Values").
    
    <a id="idm45429587138144"></a><a id="idm45429587136752"></a>
- **Optimizer. ** These optimizer enhancements were added:
    
    <a id="idm45429587133744"></a><a id="idm45429587132352"></a>
    
    - MySQL now supports invisible indexes. An invisible index is not used by the optimizer at all, but is otherwise maintained normally. Indexes are visible by default. Invisible indexes make it possible to test the effect of removing an index on query performance, without making a destructive change that must be undone should the index turn out to be required. See [Section 8.3.12, “Invisible Indexes”](https://dev.mysql.com/doc/refman/8.0/en/invisible-indexes.html "8.3.12 Invisible Indexes").
        
    - MySQL now supports descending indexes: `DESC` in an index definition is no longer ignored but causes storage of key values in descending order. Previously, indexes could be scanned in reverse order but at a performance penalty. A descending index can be scanned in forward order, which is more efficient. Descending indexes also make it possible for the optimizer to use multiple-column indexes when the most efficient scan order mixes ascending order for some columns and descending order for others. See [Section 8.3.13, “Descending Indexes”](https://dev.mysql.com/doc/refman/8.0/en/descending-indexes.html "8.3.13 Descending Indexes").
        
    - MySQL now supports creation of functional index key parts that index expression values rather than column values. Functional key parts enable indexing of values that cannot be indexed otherwise, such as [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") values. For details, see [Section 13.1.15, “CREATE INDEX Statement”](https://dev.mysql.com/doc/refman/8.0/en/create-index.html "13.1.15 CREATE INDEX Statement").
        
    - In MySQL 8.0.14 and later, trivial `WHERE` conditions arising from constant literal expressions are removed during preparation, rather than later on during optimization. Removal of the condition earlier in the process makes it possible to simplify joins for queries with outer joins having trivial conditions, such as this one:
        
        ```sql
        SELECT * FROM t1 LEFT JOIN t2 ON condition_1 WHERE condition_2 OR 0 = 1
        ```
        
        The optimizer now sees during preparation that 0 = 1 is always false, making `OR 0 = 1` redundant, and removes it, leaving this:
        
        ```sql
        SELECT * FROM t1 LEFT JOIN t2 ON condition_1 where condition_2
        ```
        
        Now the optimizer can rewrite the query as an inner join, like this:
        
        ```sql
        SELECT * FROM t1 LEFT JOIN t2 WHERE condition_1 AND condition_2
        ```
        
        For more information, see [Section 8.2.1.9, “Outer Join Optimization”](https://dev.mysql.com/doc/refman/8.0/en/outer-join-optimization.html "8.2.1.9 Outer Join Optimization").
        
    - In MySQL 8.0.16 and later, MySQL can use constant folding at optimization time to handle comparisons between a column and a constant value where the constant is out of range or on a range boundary with respect to the type of the column, rather than doing so for each row at execution time. For example, given a table `t` with a `TINYINT UNSIGNED` column `c`, the optimizer can rewrite a condition such as `WHERE c < 256` to `WHERE 1` (and optimize the condition away altogether), or `WHERE c >= 255` to `WHERE c = 255`.
        
        See [Section 8.2.1.14, “Constant-Folding Optimization”](https://dev.mysql.com/doc/refman/8.0/en/constant-folding-optimization.html "8.2.1.14 Constant-Folding Optimization"), for more information.
        
    - Beginning with MySQL 8.0.16, the semijoin optimizations used with `IN` subqueries can now be applied to `EXISTS` subqueries as well. In addition, the optimizer now decorrelates trivially-correlated equality predicates in the `WHERE` condition attached to the subquery, so that they can be treated similarly to expressions in `IN` subqueries; this applies to both `EXISTS` and `IN` subqueries.
        
        For more information, see [Section 8.2.2.1, “Optimizing IN and EXISTS Subquery Predicates with Semijoin Transformations”](https://dev.mysql.com/doc/refman/8.0/en/semijoins.html "8.2.2.1 Optimizing IN and EXISTS Subquery Predicates with Semijoin Transformations").
        
    - As of MySQL 8.0.17, the server rewrites any incomplete SQL predicates (that is, predicates having the form ``WHERE `value` ``, in which *`value`* is a column name or constant expression and no comparison operator is used) internally as ``WHERE `value` <> 0`` during the contextualization phase, so that the query resolver, query optimizer, and query executor need work only with complete predicates.
        
        One visible effect of this change is that, for Boolean values, [`EXPLAIN`](https://dev.mysql.com/doc/refman/8.0/en/explain.html "13.8.2 EXPLAIN Statement") output now shows `true` and `false`, rather than `1` and `0`.
        
        Another effect of this change is that evaluation of a JSON value in an SQL boolean context performs an implicit comparison against JSON integer 0. Consider the table created and populated as shown here:
        
        ```sql
        mysql> CREATE TABLE test (id INT, col JSON);
        
        mysql> INSERT INTO test VALUES (1, '{"val":true}'), (2, '{"val":false}');
        ```
        
        Previously, the server attempted to convert an extracted `true` or `false` value to an SQL boolean when comparing it in an SQL boolean context, as shown by the following query using `IS TRUE`:
        
        ```sql
        mysql> SELECT id, col, col->"$.val" FROM test WHERE col->"$.val" IS TRUE;
        +------+---------------+--------------+
        | id   | col           | col->"$.val" |
        +------+---------------+--------------+
        |    1 | {"val": true} | true         |
        +------+---------------+--------------+
        ```
        
        In MySQL 8.0.17 and later, the implicit copmparison of the extracted value with JSON integer 0 leads to a different result:
        
        ```sql
        mysql> SELECT id, col, col->"$.val" FROM test WHERE col->"$.val" IS TRUE;
        +------+----------------+--------------+
        | id   | col            | col->"$.val" |
        +------+----------------+--------------+
        |    1 | {"val": true}  | true         |
        |    2 | {"val": false} | false        |
        +------+----------------+--------------+
        ```
        
        Beginning with MySQL 8.0.21, you can use [`JSON_VALUE()`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#function_json-value) on the extracted value to perform type conversion prior to performing the test, as shown here:
        
        ```sql
        mysql> SELECT id, col, col->"$.val" FROM test
         ->     WHERE JSON_VALUE(col, "$.val" RETURNING UNSIGNED) IS TRUE;
        +------+---------------+--------------+
        | id   | col           | col->"$.val" |
        +------+---------------+--------------+
        |    1 | {"val": true} | true         |
        +------+---------------+--------------+
        ```
        
        Also beginning with MySQL 8.0.21, the server provides the warning Evaluating a JSON value in SQL boolean context does an implicit comparison against JSON integer 0; if this is not what you want, consider converting JSON to an SQL numeric type with JSON_VALUE RETURNING when comparing extracted values in an SQL boolean context in this manner.
        
    - In MySQL 8.0.17 and later a `WHERE` condition having ``NOT IN (`subquery`)`` or ``NOT EXISTS (`subquery`)`` is transformed internally into an antijoin. (An antijoin returns all rows from the table for which there is no row in the table to which it is joined matching the join condition.) This removes the subquery which can result in faster query execution since the subquery's tables are now handled on the top level.
        
        This is similar to, and reuses, the existing `IS NULL` (`Not exists`) optimization for outer joins; see [EXPLAIN Extra Information](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain-extra-information "EXPLAIN Extra Information").
        
    - Beginning with MySQL 8.0.21, a single-table [`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/update.html "13.2.13 UPDATE Statement") or [`DELETE`](https://dev.mysql.com/doc/refman/8.0/en/delete.html "13.2.2 DELETE Statement") statement can now in many cases make use of a semijoin transformation or subquery materialization. This applies to statements of the forms shown here:
        
        - ``UPDATE t1 SET t1.a=`value` WHERE t1.a IN (SELECT t2.a FROM t2)``
            
        - `DELETE FROM t1 WHERE t1.a IN (SELECT t2.a FROM t2)`
            
        
        This can be done for a single-table `UPDATE` or `DELETE` meeting the following conditions:
        
        - The `UPDATE` or `DELETE` statement uses a subquery having a `[NOT] IN` or `[NOT] EXISTS` predicate.
            
        - The statement has no `ORDER BY` clause, and has no `LIMIT` clause.
            
            (The multi-table versions of `UPDATE` and `DELETE` do not support `ORDER BY` or `LIMIT`.)
            
        - The target table does not support read-before-write removal (relevant only for [`NDB`](https://dev.mysql.com/doc/refman/8.0/en/mysql-cluster.html "Chapter 23 MySQL NDB Cluster 8.0") tables).
            
        - Semijoin or subquery materialization is allowed, based on any hints contained in the subquery and the value of [`optimizer_switch`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_optimizer_switch).
            
        
        When the semijoin optimization is used for an eligible single-table `DELETE` or `UPDATE`, this is visible in the optimizer trace: for a multi-table statement there is a `join_optimization` object in the trace, while there is none for a single-table statement. The conversion is also visible in the output of `EXPLAIN FORMAT=TREE` or [`EXPLAIN ANALYZE`](https://dev.mysql.com/doc/refman/8.0/en/explain.html#explain-analyze "Obtaining Information with EXPLAIN ANALYZE"); a single-table statement shows `<not executable by iterator executor>`, while a multi-table statement reports a full plan.
        
        Alo beginning with MySQL 8.0.21, semi-consistent reads are supported by multi-table `UPDATE` statements using [`InnoDB`](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html "Chapter 15 The InnoDB Storage Engine") tables, for transaction isolation levels weaker than [`REPEATABLE READ`](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html#isolevel_repeatable-read).
        
    - **Improved hash join performance. ** MySQL 8.0.23 reimplements the hash table used for hash joins, resulting in several improvements in hash join performance. This work includes a fix for an issue (Bug #31516149, Bug #99933) whereby only roughly 2/3 of the memory allocated for the join buffer ([`join_buffer_size`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_join_buffer_size)) could actually be used by a hash join.
        
        The new hash table is generally faster than the old one, and uses less memory for alignment, keys/values, and in scenarios where there are many equal keys. In addition, the server can now free old memory when the size of the hash table increases.
        
    
- **Common table expressions. ** MySQL now supports common table expressions, both nonrecursive and recursive. Common table expressions enable use of named temporary result sets, implemented by permitting a [`WITH`](https://dev.mysql.com/doc/refman/8.0/en/with.html "13.2.15 WITH (Common Table Expressions)") clause preceding [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") statements and certain other statements. For more information, see [Section 13.2.15, “WITH (Common Table Expressions)”](https://dev.mysql.com/doc/refman/8.0/en/with.html "13.2.15 WITH (Common Table Expressions)").
    
    <a id="idm45429587034992"></a><a id="idm45429587033600"></a>
    
    As of MySQL 8.0.19, the recursive [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") part of a recursive common table expression (CTE) supports a `LIMIT` clause. `LIMIT` with `OFFSET` is also supported. See [Recursive Common Table Expressions](https://dev.mysql.com/doc/refman/8.0/en/with.html#common-table-expressions-recursive "Recursive Common Table Expressions"), for more information.
    
- **Window functions. ** MySQL now supports window functions that, for each row from a query, perform a calculation using rows related to that row. These include functions such as [`RANK()`](https://dev.mysql.com/doc/refman/8.0/en/window-function-descriptions.html#function_rank), [`LAG()`](https://dev.mysql.com/doc/refman/8.0/en/window-function-descriptions.html#function_lag), and [`NTILE()`](https://dev.mysql.com/doc/refman/8.0/en/window-function-descriptions.html#function_ntile). In addition, several existing aggregate functions now can be used as window functions (for example, [`SUM()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_sum) and [`AVG()`](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_avg)). For more information, see [Section 12.21, “Window Functions”](https://dev.mysql.com/doc/refman/8.0/en/window-functions.html "12.21 Window Functions").
    
    <a id="idm45429587020912"></a><a id="idm45429587019520"></a>
- **Lateral derived tables. ** A derived table now may be preceded by the `LATERAL` keyword to specify that it is permitted to refer to (depend on) columns of preceding tables in the same `FROM` clause. Lateral derived tables make possible certain SQL operations that cannot be done with nonlateral derived tables or that require less-efficient workarounds. See [Section 13.2.11.9, “Lateral Derived Tables”](https://dev.mysql.com/doc/refman/8.0/en/lateral-derived-tables.html "13.2.11.9 Lateral Derived Tables").
    
    <a id="idm45429587014368"></a><a id="idm45429587012976"></a>
- **Aliases in single-table DELETE statements. ** In MySQL 8.0.16 and later, single-table [`DELETE`](https://dev.mysql.com/doc/refman/8.0/en/delete.html "13.2.2 DELETE Statement") statements support the use of table aliases.
    
    <a id="idm45429587008880"></a><a id="idm45429587007488"></a>
- **Regular expression support. ** Previously, MySQL used the Henry Spencer regular expression library to support regular expression operators ([`REGEXP`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#operator_regexp), [`RLIKE`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#operator_regexp)). Regular expression support has been reimplemented using International Components for Unicode (ICU), which provides full Unicode support and is multibyte safe. The [`REGEXP_LIKE()`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#function_regexp-like) function performs regular expression matching in the manner of the [`REGEXP`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#operator_regexp) and [`RLIKE`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#operator_regexp) operators, which now are synonyms for that function. In addition, the [`REGEXP_INSTR()`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#function_regexp-instr), [`REGEXP_REPLACE()`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#function_regexp-replace), and [`REGEXP_SUBSTR()`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#function_regexp-substr) functions are available to find match positions and perform substring substitution and extraction, respectively. The [`regexp_stack_limit`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_regexp_stack_limit) and [`regexp_time_limit`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_regexp_time_limit) system variables provide control over resource consumption by the match engine. For more information, see [Section 12.8.2, “Regular Expressions”](https://dev.mysql.com/doc/refman/8.0/en/regexp.html "12.8.2 Regular Expressions"). For information about ways in which applications that use regular expressions may be affected by the implementation change, see [Regular Expression Compatibility Considerations](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#regexp-compatibility "Regular Expression Compatibility Considerations").
    
    <a id="idm45429586992464"></a><a id="idm45429586991072"></a>
- **Internal temporary tables. ** The `TempTable` storage engine replaces the `MEMORY` storage engine as the default engine for in-memory internal temporary tables. The `TempTable` storage engine provides efficient storage for [`VARCHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html "11.3.2 The CHAR and VARCHAR Types") and [`VARBINARY`](https://dev.mysql.com/doc/refman/8.0/en/binary-varbinary.html "11.3.3 The BINARY and VARBINARY Types") columns. The [`internal_tmp_mem_storage_engine`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_internal_tmp_mem_storage_engine) session variable defines the storage engine for in-memory internal temporary tables. Permitted values are `TempTable` (the default) and `MEMORY`. The [`temptable_max_ram`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_temptable_max_ram) variable defines the maximum amount of memory that the `TempTable` storage engine can use before data is stored to disk.
    
    <a id="idm45429586979856"></a><a id="idm45429586978464"></a>
- **Logging. ** Error logging was rewritten to use the MySQL component architecture. Traditional error logging is implemented using built-in components, and logging using the system log is implemented as a loadable component. In addition, a loadable JSON log writer is available. To control which log components to enable, use the [`log_error_services`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_log_error_services) system variable. For more information, see [Section 5.4.2, “The Error Log”](https://dev.mysql.com/doc/refman/8.0/en/error-log.html "5.4.2 The Error Log").
    
    <a id="idm45429586973520"></a><a id="idm45429586972128"></a>
- **Backup lock. ** A new type of backup lock permits DML during an online backup while preventing operations that could result in an inconsistent snapshot. The new backup lock is supported by [`LOCK INSTANCE FOR BACKUP`](https://dev.mysql.com/doc/refman/8.0/en/lock-instance-for-backup.html "13.3.5 LOCK INSTANCE FOR BACKUP and UNLOCK INSTANCE Statements") and [`UNLOCK INSTANCE`](https://dev.mysql.com/doc/refman/8.0/en/lock-instance-for-backup.html "13.3.5 LOCK INSTANCE FOR BACKUP and UNLOCK INSTANCE Statements") syntax. The [`BACKUP_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_backup-admin) privilege is required to use these statements.
    
    <a id="idm45429586965888"></a><a id="idm45429586964496"></a>
- **Replication. ** The following enhancements have been made to MySQL Replication:
    
    <a id="idm45429586961440"></a><a id="idm45429586960048"></a>
    
    - MySQL Replication now supports binary logging of partial updates to JSON documents using a compact binary format, saving space in the log over logging complete JSON documents. Such compact logging is done automatically when statement-based logging is in use, and can be enabled by setting the new `binlog_row_value_options` system variable to `PARTIAL_JSON`. For more information, see [Partial Updates of JSON Values](https://dev.mysql.com/doc/refman/8.0/en/json.html#json-partial-updates "Partial Updates of JSON Values"), as well as the description of [`binlog_row_value_options`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_binlog_row_value_options).
        
    
- **Connection management. ** MySQL Server now permits a TCP/IP port to be configured specifically for administrative connections. This provides an alternative to the single administrative connection that is permitted on the network interfaces used for ordinary connections even when [`max_connections`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_connections) connections are already established. See [Section 5.1.12.1, “Connection Interfaces”](https://dev.mysql.com/doc/refman/8.0/en/connection-interfaces.html "5.1.12.1 Connection Interfaces").
    
    <a id="idm45429586950800"></a><a id="idm45429586949408"></a>
    
    MySQL now provides more control over the use of compression to minimize the number of bytes sent over connections to the server. Previously, a given connection was either uncompressed or used the `zlib` compression algorithm. Now, it is also possible to use the `zstd` algorithm, and to select a compression level for `zstd` connections. The permitted compression algorithms can be configured on the server side, as well as on the connection-origination side for connections by client programs and by servers participating in source/replica replication or Group Replication. For more information, see [Section 4.2.8, “Connection Compression Control”](https://dev.mysql.com/doc/refman/8.0/en/connection-compression-control.html "4.2.8 Connection Compression Control").
    
- **Configuration. ** The maximum permitted length of host names throughout MySQL has been raised to 255 ASCII characters, up from the previous limit of 60 characters. This applies to, for example, host name-related columns in the data dictionary, `mysql` system schema, Performance Schema, `INFORMATION_SCHEMA`, and `sys` schema; the `MASTER_HOST` value for the [`CHANGE MASTER TO`](https://dev.mysql.com/doc/refman/8.0/en/change-master-to.html "13.4.2.1 CHANGE MASTER TO Statement") statement; the `Host` column in [`SHOW PROCESSLIST`](https://dev.mysql.com/doc/refman/8.0/en/show-processlist.html "13.7.7.29 SHOW PROCESSLIST Statement") statement output; host names in account names (such as used in account-management statements and in `DEFINER` attributes); and host name-related command options and system variables.
    
    <a id="idm45429586936608"></a><a id="idm45429586935216"></a><a id="idm45429586933824"></a>
    
    Caveats:
    
    - The increase in permitted host name length can affect tables with indexes on host name columns. For example, tables in the `mysql` system schema that index host names now have an explicit `ROW_FORMAT` attribute of `DYNAMIC` to accommodate longer index values.
        
    - Some file name-valued configuration settings might be constructed based on the server host name. The permitted values are constrained by the underlying operating system, which may not permit file names long enough to include 255-character host names. This affects the [`general_log_file`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_general_log_file), [`log_error`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_log_error), [`pid_file`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_pid_file), [`relay_log`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#sysvar_relay_log), and [`slow_query_log_file`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_slow_query_log_file) system variables and corresponding options. If host name-based values are too long for the OS, explicit shorter values must be provided.
        
    - Although the server now supports 255-character host names, connections to the server established using the [`--ssl-mode=VERIFY_IDENTITY`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-mode) option are constrained by maximum host name length supported by OpenSSL. Host name matches pertain to two fields of SSL certificates, which have maximum lengths as follows: Common Name: maximum length 64; Subject Alternative Name: maximum length as per RFC#1034.
        
    
- **Plugins. ** Previously, MySQL plugins could be written in C or C++. MySQL header files used by plugins now contain C++ code, which means that plugins must be written in C++, not C.
    
    <a id="idm45429586918464"></a><a id="idm45429586917072"></a>
- **C API. ** The MySQL C API now supports asynchronous functions for nonblocking communication with the MySQL server. Each function is the asynchronous counterpart to an existing synchronous function. The synchronous functions block if reads from or writes to the server connection must wait. The asynchronous functions enable an application to check whether work on the server connection is ready to proceed. If not, the application can perform other work before checking again later. See [C API Asynchronous Interface](https://dev.mysql.com/doc/c-api/8.0/en/c-api-asynchronous-interface.html).
    
    <a id="idm45429586913424"></a><a id="idm45429586912032"></a>
- **Additional target types for casts. ** The functions [`CAST()`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#function_cast) and [`CONVERT()`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#function_convert) now support conversions to types [`DOUBLE`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE"), [`FLOAT`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE"), and [`REAL`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE"). Added in MySQL 8.0.17. See [Section 12.11, “Cast Functions and Operators”](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html "12.11 Cast Functions and Operators").
    
    <a id="idm45429586903568"></a><a id="idm45429586902176"></a>
- **JSON schema validation. ** MySQL 8.0.17 adds two functions [`JSON_SCHEMA_VALID()`](https://dev.mysql.com/doc/refman/8.0/en/json-validation-functions.html#function_json-schema-valid) and [`JSON_SCHEMA_VALIDATION_REPORT()`](https://dev.mysql.com/doc/refman/8.0/en/json-validation-functions.html#function_json-schema-validation-report) for validating JSON documents again JSON schemas. `JSON_SCHEMA_VALID()` returns TRUE (1) if the document validates against the schema and FALSE (0) if it does not. `JSON_SCHEMA_VALIDATION_REPORT()` returns a JSON document containing detailed information about the results of the validation. The following statements apply to both of these functions:
    
    <a id="idm45429586895472"></a><a id="idm45429586894080"></a>
    
    - The schema must conform to Draft 4 of the JSON Schema specification.
        
    - `required` attributes are supported.
        
    - External resources and the `$ref` keyword are not supported.
        
    - Regular expression patterns are supported; invalid patterns are silently ignored.
        
    
    See [Section 12.18.7, “JSON Schema Validation Functions”](https://dev.mysql.com/doc/refman/8.0/en/json-validation-functions.html "12.18.7 JSON Schema Validation Functions"), for more information and examples.
    
- **Multi-valued indexes. ** Beginning with MySQL 8.0.17, [`InnoDB`](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html "Chapter 15 The InnoDB Storage Engine") supports the creation of a multi-valued index, which is a secondary index defined on a [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") column that stores an array of values and which can have multiple index records for a single data record. Such an index uses a key part definition such as `CAST(data->'$.zipcode' AS UNSIGNED ARRAY)`. A multi-valued index is used automatically by the MySQL optimizer for suitable queries, as can be viewed in the output of [`EXPLAIN`](https://dev.mysql.com/doc/refman/8.0/en/explain.html "13.8.2 EXPLAIN Statement").
    
    <a id="idm45429586880784"></a><a id="idm45429586879392"></a>
    
    As part of this work, MySQL adds a new function [`JSON_OVERLAPS()`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#function_json-overlaps) and a new [`MEMBER OF()`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#operator_member-of) operator for working with [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") documents, additionally extending the [`CAST()`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#function_cast) function with a new `ARRAY` keyword, as described in the following list:
    
    - `JSON_OVERLAPS()` compares two [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") documents. If they contain any key-value pairs or array elements in common, the function returns TRUE (1); otherwise it returns FALSE (0). If both values are scalars, the function performs a simple test for equality. If one argument is a JSON array and the other is a scalar, the scalar is treated as an array element. Thus, `JSON_OVERLAPS()` acts as a complement to [`JSON_CONTAINS()`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#function_json-contains).
        
    - `MEMBER OF()` tests whether the first operand (a scalar or JSON document) is a member of the JSON array passed as the second operand, returning TRUE (1) if it is, and FALSE (0) if it is not. No type conversion of the operand is performed.
        
    - ``CAST(`expression` AS `type` ARRAY)`` permits creation of a functional index by casting the JSON array found in a JSON document at *`json_path`* to an SQL array. Type specifiers are limited to those already supported by `CAST()`, with the exception of `BINARY` (not supported). This usage of `CAST()` (and the `ARRAY` keyword) is supported only by [`InnoDB`](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html "Chapter 15 The InnoDB Storage Engine"), and only for the creation of a multi-valued index.
        
    
    For detailed information about multi-valued indexes, including examples, see [Multi-Valued Indexes](https://dev.mysql.com/doc/refman/8.0/en/create-index.html#create-index-multi-valued "Multi-Valued Indexes"). [Section 12.18.3, “Functions That Search JSON Values”](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html "12.18.3 Functions That Search JSON Values"), provides information about `JSON_OVERLAPS()` and `MEMBER OF()`, along with examples of use.
    
- **Hintable time_zone. ** As of MySQL 8.0.17, the [`time_zone`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_time_zone) session variable is hintable using [`SET_VAR`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-set-var "Variable-Setting Hint Syntax").
    
    <a id="idm45429586852976"></a><a id="idm45429586851584"></a>
- **Redo Log Archiving. ** As of MySQL 8.0.17, `InnoDB` supports redo log archiving. Backup utilities that copy redo log records may sometimes fail to keep pace with redo log generation while a backup operation is in progress, resulting in lost redo log records due to those records being overwritten. The redo log archiving feature addresses this issue by sequentially writing redo log records to an archive file. Backup utilities can copy redo log records from the archive file as necessary, thereby avoiding the potential loss of data. For more information, see [Redo Log Archiving](https://dev.mysql.com/doc/refman/8.0/en/innodb-redo-log.html#innodb-redo-log-archiving "Redo Log Archiving").
    
    <a id="idm45429586847536"></a><a id="idm45429586846144"></a>
- **The Clone Plugin. ** As of MySQL 8.0.17, MySQL provides a clone plugin that permits cloning `InnoDB` data locally or from a remote MySQL server instance. A local cloning operation stores cloned data on the same server or node where the MySQL instance runs. A remote cloning operation transfers cloned data over the network from a donor MySQL server instance to the recipient server or node where the cloning operation was initiated.
    
    <a id="idm45429586842032"></a><a id="idm45429586840640"></a>
    
    The clone plugin supports replication. In addition to cloning data, a cloning operation extracts and transfers replication coordinates from the donor and applies them on the recipient, which enables using the clone plugin for provisioning Group Replication members and replicas. Using the clone plugin for provisioning is considerably faster and more efficient than replicating a large number of transactions. Group Replication members can also be configured to use the clone plugin as an alternative method of recovery, so that members automatically choose the most efficient way to retrieve group data from seed members.
    
    For more information, see [Section 5.6.7, “The Clone Plugin”](https://dev.mysql.com/doc/refman/8.0/en/clone-plugin.html "5.6.7 The Clone Plugin"), and [Section 18.5.3.2, “Cloning for Distributed Recovery”](https://dev.mysql.com/doc/refman/8.0/en/group-replication-cloning.html "18.5.3.2 Cloning for Distributed Recovery").
    
    As of MySQL 8.0.27, concurrent DDL operations on the donor MySQL Server instance are permitted while a cloning operation is in progress. Previously, a backup lock was held during the cloning operation, preventing concurrent DDL on the donor. To revert to the previous behavior of blocking concurrent DDL on the donor during a clone operation, enable the [`clone_block_ddl`](https://dev.mysql.com/doc/refman/8.0/en/clone-plugin-options-variables.html#sysvar_clone_block_ddl) variable. See [Section 5.6.7.4, “Cloning and Concurrent DDL”](https://dev.mysql.com/doc/refman/8.0/en/clone-plugin-concurrent-ddl.html "5.6.7.4 Cloning and Concurrent DDL").
    
- **Hash Join Optimization. ** Beginning with MySQL 8.0.18, a hash join is used whenever each pair of tables in a join includes at least one equi-join condition, and no indexes apply to any join condition. A hash join does not require indexes, although it can be used with indexes applying to single-table predicates only. A hash join is more efficient in most cases than the block-nested loop algorithm. Joins such as those shown here can be optimized in this manner:
    
    <a id="idm45429586833184"></a><a id="idm45429586831792"></a>
    
    ```sql
    SELECT *
        FROM t1
        JOIN t2
            ON t1.c1=t2.c1;
    
    SELECT *
        FROM t1
        JOIN t2
            ON (t1.c1 = t2.c1 AND t1.c2 < t2.c2)
        JOIN t3
            ON (t2.c1 = t3.c1)
    ```
    
    Hash joins can also be used for Cartesian products—that is, when no join condition is specified.
    
    You can see when the hash join optimization is being used for a particular query using [`EXPLAIN FORMAT=TREE`](https://dev.mysql.com/doc/refman/8.0/en/explain.html "13.8.2 EXPLAIN Statement") or [`EXPLAIN ANALYZE`](https://dev.mysql.com/doc/refman/8.0/en/explain.html#explain-analyze "Obtaining Information with EXPLAIN ANALYZE"). (In MySQL 8.0.20 and later, you can also use `EXPLAIN`, omitting `FORMAT=TREE`.)
    
    The amount of memory available to a hash join is limited by the value of [`join_buffer_size`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_join_buffer_size). A hash join that requires more than this much memory is executed on disk; the number of disk files that can be used by an on-disk hash join is limited by [`open_files_limit`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_open_files_limit).
    
    As of MySQL 8.0.19, the [`hash_join`](https://dev.mysql.com/doc/refman/8.0/en/switchable-optimizations.html#optflag_hash-join) optimizer switch which was introduced in MySQL 8.0.18 no longer supported (hash\_join=on still appears as part of the value of optimizer\_switch, but setting it no longer has any effect). The [`HASH_JOIN`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level "Table-Level Optimizer Hints") and `NO_HASH_JOIN` optimizer hints are also no longer supported. The switch and the hint are both now deprecated; expect them to be removed in a future MySQL release. In MySQL 8.0.18 and later, hash joins can be disabled using the [`NO_BNL`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level "Table-Level Optimizer Hints") optimizer switch.
    
    In MySQL 8.0.20 and later, block nested loop is no longer used in the MySQL server, and a hash join is employed any time a block nested loop would have been used previously, even when the query contains no equi-join conditions. This applies to inner non-equijoins, semijoins, antijoins, left outer joins, and right outer joins. TThe [`block_nested_loop`](https://dev.mysql.com/doc/refman/8.0/en/switchable-optimizations.html#optflag_block-nested-loop) flag for the [`optimizer_switch`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_optimizer_switch) system variable as well as the [`BNL`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level "Table-Level Optimizer Hints") and `NO_BNL` optimizer hints are still supported, but henceforth control use of hash joins only. In addition, both inner and outer joins (including semijoins and antijoins) can now employ batched key access (BKA), which allocates join buffer memory incrementally so that individual queries need not use up large amounts of resources that they do not actually require for resolution. BKA for inner joins only is supported starting with MySQL 8.0.18.
    
    MySQL 8.0.20 also replaces the executor used in previous versions of MySQL with the iterator executor. This work includes replacement of the old index subquery engines that governed queries of the form ``WHERE `value` IN (SELECT `column` FROM `table` WHERE ...)`` for those `IN` queries which have not been optimized as semijoins, as well as queries materialized in the same form, which formerly depended on the old executor.
    
    For more information and examples, see [Section 8.2.1.4, “Hash Join Optimization”](https://dev.mysql.com/doc/refman/8.0/en/hash-joins.html "8.2.1.4 Hash Join Optimization"). See also [Batched Key Access Joins](https://dev.mysql.com/doc/refman/8.0/en/bnl-bka-optimization.html#bka-optimization "Batched Key Access Joins").
    
- **EXPLAIN ANALYZE Statement. ** A new form of the [`EXPLAIN`](https://dev.mysql.com/doc/refman/8.0/en/explain.html "13.8.2 EXPLAIN Statement") statement, [`EXPLAIN ANALYZE`](https://dev.mysql.com/doc/refman/8.0/en/explain.html#explain-analyze "Obtaining Information with EXPLAIN ANALYZE"), is implemented in MySQL 8.0.18, providing expanded information about the execution of [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") statements in `TREE` format for each iterator used in processing the query, and making it possible to compare estimated cost with the actual cost of the query. This information includes startup cost, total cost, number of rows returned by this iterator, and the number of loops executed.
    
    In MySQL 8.0.21 and later, this statement also supports a `FORMAT=TREE` specifier. `TREE` is the only supported format.
    
    <a id="idm45429586799968"></a><a id="idm45429586798576"></a>
    
    See [Obtaining Information with EXPLAIN ANALYZE](https://dev.mysql.com/doc/refman/8.0/en/explain.html#explain-analyze "Obtaining Information with EXPLAIN ANALYZE"), for more information.
    
- **Query cast injection. ** In version 8.0.18 and later, MySQL injects cast operations into the query item tree inside expressions and conditions in which the data type of the argument and the expected data type do not match. This has no effect on query results or speed of execution, but makes the query as executed equivalent to one which is compliant with the SQL standard while maintaining backwards compatibility with previous releases of MySQL.
    
    <a id="idm45429586793648"></a><a id="idm45429586792256"></a>
    
    Such implicit casts are now performed between temporal types ([`DATE`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types"), [`DATETIME`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types"), [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types"), [`TIME`](https://dev.mysql.com/doc/refman/8.0/en/time.html "11.2.3 The TIME Type")) and numeric types ([`SMALLINT`](https://dev.mysql.com/doc/refman/8.0/en/integer-types.html "11.1.2 Integer Types (Exact Value) - INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT"), [`TINYINT`](https://dev.mysql.com/doc/refman/8.0/en/integer-types.html "11.1.2 Integer Types (Exact Value) - INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT"), [`MEDIUMINT`](https://dev.mysql.com/doc/refman/8.0/en/integer-types.html "11.1.2 Integer Types (Exact Value) - INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT"), [`INT`](https://dev.mysql.com/doc/refman/8.0/en/integer-types.html "11.1.2 Integer Types (Exact Value) - INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT")/[`INTEGER`](https://dev.mysql.com/doc/refman/8.0/en/integer-types.html "11.1.2 Integer Types (Exact Value) - INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT"), [`BIGINT`](https://dev.mysql.com/doc/refman/8.0/en/integer-types.html "11.1.2 Integer Types (Exact Value) - INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT"); [`DECIMAL`](https://dev.mysql.com/doc/refman/8.0/en/fixed-point-types.html "11.1.3 Fixed-Point Types (Exact Value) - DECIMAL, NUMERIC")/[`NUMERIC`](https://dev.mysql.com/doc/refman/8.0/en/fixed-point-types.html "11.1.3 Fixed-Point Types (Exact Value) - DECIMAL, NUMERIC"); [`FLOAT`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE"), [`DOUBLE`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE"), [`REAL`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE"); [`BIT`](https://dev.mysql.com/doc/refman/8.0/en/bit-type.html "11.1.5 Bit-Value Type - BIT")) whenever they are compared using any of the standard numeric comparison operators ([`=`](https://dev.mysql.com/doc/refman/8.0/en/assignment-operators.html#operator_assign-equal), [`>=`](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_greater-than-or-equal), [`>`](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_greater-than), [`<`](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_less-than), [`<=`](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_less-than-or-equal), [`<>`](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_not-equal)/[`!=`](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_not-equal), or [`<=>`](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_equal-to)). In this case, any value that is not already a `DOUBLE` is cast as one. Cast injection is also now performed for comparisons between [`DATE`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types") or [`TIME`](https://dev.mysql.com/doc/refman/8.0/en/time.html "11.2.3 The TIME Type") values and [`DATETIME`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types") values, where the arguments are cast whenever necessary as `DATETIME`.
    
    Beginning with MySQL 8.0.21, such casts are also performed when comparing string types with other types. String types that are cast include [`CHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html "11.3.2 The CHAR and VARCHAR Types"), [`VARCHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html "11.3.2 The CHAR and VARCHAR Types"), [`BINARY`](https://dev.mysql.com/doc/refman/8.0/en/binary-varbinary.html "11.3.3 The BINARY and VARBINARY Types"), [`VARBINARY`](https://dev.mysql.com/doc/refman/8.0/en/binary-varbinary.html "11.3.3 The BINARY and VARBINARY Types"), [`BLOB`](https://dev.mysql.com/doc/refman/8.0/en/blob.html "11.3.4 The BLOB and TEXT Types"), [`TEXT`](https://dev.mysql.com/doc/refman/8.0/en/blob.html "11.3.4 The BLOB and TEXT Types"), [`ENUM`](https://dev.mysql.com/doc/refman/8.0/en/enum.html "11.3.5 The ENUM Type"), and [`SET`](https://dev.mysql.com/doc/refman/8.0/en/set.html "11.3.6 The SET Type"). When comparing a value of a string type with a numeric type or `YEAR`, the string cast is to `DOUBLE`; if the type of the other argument is not `FLOAT`, `DOUBLE`, or `REAL`, it is also cast to `DOUBLE`. When comparing a string type to a `DATETIME` or `TIMESTAMP` value, the string is cast is to `DATETIME`; when comparing a string type with `DATE`, the string is cast to `DATE`.
    
    It is possible to see when casts are injected into a given query by viewing the output of [`EXPLAIN ANALYZE`](https://dev.mysql.com/doc/refman/8.0/en/explain.html#explain-analyze "Obtaining Information with EXPLAIN ANALYZE"), `EXPLAIN FORMAT=JSON`, or, as shown here, `EXPLAIN FORMAT=TREE`:
    
    ```sql
    mysql> CREATE TABLE d (dt DATETIME, d DATE, t TIME);
    Query OK, 0 rows affected (0.62 sec)
    
    mysql> CREATE TABLE n (i INT, d DECIMAL, f FLOAT, dc DECIMAL);
    Query OK, 0 rows affected (0.51 sec)
    
    mysql> CREATE TABLE s (c CHAR(25), vc VARCHAR(25),
     ->     bn BINARY(50), vb VARBINARY(50), b BLOB, t TEXT,
     ->     e ENUM('a', 'b', 'c'), se SET('x' ,'y', 'z'));
    Query OK, 0 rows affected (0.50 sec)
    
    mysql> EXPLAIN FORMAT=TREE SELECT * from d JOIN n ON d.dt = n.i\G
    *************************** 1. row ***************************
    EXPLAIN: -> Inner hash join (cast(d.dt as double) = cast(n.i as double))
    (cost=0.70 rows=1)
     -> Table scan on n  (cost=0.35 rows=1)
     -> Hash
     -> Table scan on d  (cost=0.35 rows=1)
    
    mysql> EXPLAIN FORMAT=TREE SELECT * from s JOIN d ON d.dt = s.c\G
    *************************** 1. row ***************************
    EXPLAIN: -> Inner hash join (d.dt = cast(s.c as datetime(6)))  (cost=0.72 rows=1)
     -> Table scan on d  (cost=0.37 rows=1)
     -> Hash
     -> Table scan on s  (cost=0.35 rows=1)
    
    1 row in set (0.01 sec)
    
    mysql> EXPLAIN FORMAT=TREE SELECT * from n JOIN s ON n.d = s.c\G
    *************************** 1. row ***************************
    EXPLAIN: -> Inner hash join (cast(n.d as double) = cast(s.c as double))  (cost=0.70 rows=1)
     -> Table scan on s  (cost=0.35 rows=1)
     -> Hash
     -> Table scan on n  (cost=0.35 rows=1)
    
    1 row in set (0.00 sec)
    ```
    
    Such casts can also be seen by executing `EXPLAIN [FORMAT=TRADITIONAL]`, in which case it is also necessary to issue [`SHOW WARNINGS`](https://dev.mysql.com/doc/refman/8.0/en/show-warnings.html "13.7.7.42 SHOW WARNINGS Statement") after executing the `EXPLAIN` statement.
    
- **Time zone support for TIMESTAMP and DATETIME. ** As of MySQL 8.0.19, the server accepts a time zone offset with inserted datetime ([`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types") and [`DATETIME`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types")) values. This offset uses the same format as that employed when setting the [`time_zone`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_time_zone) system variable, except that a leading zero is required when the hours portion of the offset is less than 10, and `'-00:00'` is not allowed. Examples of datetime literals that include time zone offsets are `'2019-12-11 10:40:30-05:00'`, `'2003-04-14 03:30:00+10:00'`, and `'2020-01-01 15:35:45+05:30'`.
    
    <a id="idm45429586713392"></a><a id="idm45429586711904"></a>
    
    Time zone offsets are not displayed when selecting datetime values.
    
    Datetime literals incorporating time zone offsets can be used as prepared statement parameter values.
    
    As part of this work, the value used to set the [`time_zone`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_time_zone) system variable is now also restricted to the range `-13:59` to `+14:00`, inclusive. (It remains possible to assign name values to `time_zone` such as `'EST'`, `'Posix/Australia/Brisbane'`, and `'Europe/Stockholm'` to this variable, provided that the MySQL time zone tables are loaded; see [Populating the Time Zone Tables](https://dev.mysql.com/doc/refman/8.0/en/time-zone-support.html#time-zone-installation "Populating the Time Zone Tables")).
    
    For more information and examples, see [Section 5.1.15, “MySQL Server Time Zone Support”](https://dev.mysql.com/doc/refman/8.0/en/time-zone-support.html "5.1.15 MySQL Server Time Zone Support"), as well as [Section 11.2.2, “The DATE, DATETIME, and TIMESTAMP Types”](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types").
    
- **Precise information for JSON schema CHECK constraint failures. ** When using [`JSON_SCHEMA_VALID()`](https://dev.mysql.com/doc/refman/8.0/en/json-validation-functions.html#function_json-schema-valid) to specify a `CHECK` constraint, MySQL 8.0.19 and later provides precise information about the reasons for failures of such constraints.
    
    <a id="idm45429586696672"></a><a id="idm45429586695168"></a>
    
    For examples and more information, see [JSON\_SCHEMA\_VALID() and CHECK constraints](https://dev.mysql.com/doc/refman/8.0/en/json-validation-functions.html#json-validation-functions-constraints "JSON_SCHEMA_VALID() and CHECK constraints"). See also [Section 13.1.20.6, “CHECK Constraints”](https://dev.mysql.com/doc/refman/8.0/en/create-table-check-constraints.html "13.1.20.6 CHECK Constraints").
    
- **Row and column aliases with ON DUPLICATE KEY UPDATE. ** Beginning with MySQL 8.0.19, it is possible to reference the row to be inserted, and, optionally, its columns, using aliases. Consider the following [`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/insert.html "13.2.6 INSERT Statement") statement on a table `t` having columns `a` and `b`:
    
    <a id="idm45429586686320"></a><a id="idm45429586684832"></a>
    
    ```sql
    INSERT INTO t SET a=9,b=5
        ON DUPLICATE KEY UPDATE a=VALUES(a)+VALUES(b);
    ```
    
    Using the alias `new` for the new row, and, in some cases, the aliases `m` and `n` for this row's columns, the `INSERT` statement can be rewritten in many different ways, some examples of which are shown here:
    
    ```sql
    INSERT INTO t SET a=9,b=5 AS new
        ON DUPLICATE KEY UPDATE a=new.a+new.b;
    
    INSERT INTO t VALUES(9,5) AS new
        ON DUPLICATE KEY UPDATE a=new.a+new.b;
    
    INSERT INTO t SET a=9,b=5 AS new(m,n)
        ON DUPLICATE KEY UPDATE a=m+n;
    
    INSERT INTO t VALUES(9,5) AS new(m,n)
        ON DUPLICATE KEY UPDATE a=m+n;
    ```
    
    For more information and examples, see [Section 13.2.6.2, “INSERT ... ON DUPLICATE KEY UPDATE Statement”](https://dev.mysql.com/doc/refman/8.0/en/insert-on-duplicate.html "13.2.6.2 INSERT ... ON DUPLICATE KEY UPDATE Statement").
    
- **SQL standard explicit table clause and table value constructor. ** Added table value constructors and explicit table clauses according to the SQL standard. These are implemented in MySQL 8.0.19, respectively, as the [`TABLE`](https://dev.mysql.com/doc/refman/8.0/en/table.html "13.2.12 TABLE Statement") statement and the [`VALUES`](https://dev.mysql.com/doc/refman/8.0/en/values.html "13.2.14 VALUES Statement") statement.
    
    <a id="idm45429586671696"></a><a id="idm45429586670208"></a><a id="idm45429586668720"></a><a id="idm45429586667232"></a>
    
    The [`TABLE`](https://dev.mysql.com/doc/refman/8.0/en/table.html "13.2.12 TABLE Statement") statement has the format ``TABLE `table_name` ``, and is equivalent to ``SELECT * FROM `table_name` ``. It supports `ORDER BY` and `LIMIT` clauses ( the latter with optional `OFFSET`), but does not allow for the selection of individual table columns. `TABLE` can be used anywhere that you would employ the equivalent [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") statement; this includes joins, unions, [`INSERT ... SELECT`](https://dev.mysql.com/doc/refman/8.0/en/insert-select.html "13.2.6.1 INSERT ... SELECT Statement"), [`REPLACE`](https://dev.mysql.com/doc/refman/8.0/en/replace.html "13.2.9 REPLACE Statement"), [`CREATE TABLE ... SELECT`](https://dev.mysql.com/doc/refman/8.0/en/create-table-select.html "13.1.20.4 CREATE TABLE ... SELECT Statement") statements, and subqueries. For example:
    
    - `TABLE t1 UNION TABLE t2` is equivalent to `SELECT * FROM t1 UNION SELECT * FROM t2`
        
    - `CREATE TABLE t2 TABLE t1` is equivalent to `CREATE TABLE t2 SELECT * FROM t1`
        
    - `SELECT a FROM t1 WHERE b > ANY (TABLE t2)` is equivalent to `SELECT a FROM t1 WHERE b > ANY (SELECT * FROM t2)`.
        
    
    [`VALUES`](https://dev.mysql.com/doc/refman/8.0/en/values.html "13.2.14 VALUES Statement") can be used to supply a table value to an [`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/insert.html "13.2.6 INSERT Statement"), [`REPLACE`](https://dev.mysql.com/doc/refman/8.0/en/replace.html "13.2.9 REPLACE Statement"), or [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") statement, and consists of the `VALUES` keyword followed by a series of row constructors (`ROW()`) separated by commas. For example, the statement `INSERT INTO t1 VALUES ROW(1,2,3), ROW(4,5,6), ROW(7,8,9)` provides an SQL-compliant equivalent to the MySQL-specific `INSERT INTO t1 VALUES (1,2,3), (4,5,6), (7,8,9)`. You can also select from a [`VALUES`](https://dev.mysql.com/doc/refman/8.0/en/values.html "13.2.14 VALUES Statement") table value constructor just as you would a table, bearing in mind that you must supply a table alias when doing so, and use this [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") just as you would any other; this includes joins, unions, and subqueries.
    
    For more information about `TABLE` and `VALUES`, and for examples of their use, see the following sections of this documentation:
    
    - [Section 13.2.12, “TABLE Statement”](https://dev.mysql.com/doc/refman/8.0/en/table.html "13.2.12 TABLE Statement")
        
    - [Section 13.2.14, “VALUES Statement”](https://dev.mysql.com/doc/refman/8.0/en/values.html "13.2.14 VALUES Statement")
        
    - [Section 13.1.20.4, “CREATE TABLE ... SELECT Statement”](https://dev.mysql.com/doc/refman/8.0/en/create-table-select.html "13.1.20.4 CREATE TABLE ... SELECT Statement")
        
    - [Section 13.2.6.1, “INSERT ... SELECT Statement”](https://dev.mysql.com/doc/refman/8.0/en/insert-select.html "13.2.6.1 INSERT ... SELECT Statement")
        
    - [Section 13.2.10.2, “JOIN Clause”](https://dev.mysql.com/doc/refman/8.0/en/join.html "13.2.10.2 JOIN Clause")
        
    - [Section 13.2.11, “Subqueries”](https://dev.mysql.com/doc/refman/8.0/en/subqueries.html "13.2.11 Subqueries")
        
    - [Section 13.2.10.3, “UNION Clause”](https://dev.mysql.com/doc/refman/8.0/en/union.html "13.2.10.3 UNION Clause")
        
    
- **Optimizer hints for FORCE INDEX, IGNORE INDEX. ** MySQL 8.0 introduces index-level optimizer hints which serve as analogs to the traditional index hints as described in [Section 8.9.4, “Index Hints”](https://dev.mysql.com/doc/refman/8.0/en/index-hints.html "8.9.4 Index Hints"). The new hints are listed here, along with their `FORCE INDEX` or `IGNORE INDEX` equivalents:
    
    - [`GROUP_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"): Equivalent to `FORCE INDEX FOR GROUP BY`
        
        [`NO_GROUP_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"): Equivalent to `IGNORE INDEX FOR GROUP BY`
        
    - [`JOIN_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"): Equivalent to `FORCE INDEX FOR JOIN`
        
        [`NO_JOIN_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"): Equivalent to `IGNORE INDEX FOR JOIN`
        
    - [`ORDER_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"): Equivalent to `FORCE INDEX FOR ORDER BY`
        
        [`NO_ORDER_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"): Equivalent to `IGNORE INDEX FOR ORDER BY`
        
    - [`INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"): Same as [`GROUP_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints") plus [`JOIN_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints") plus [`ORDER_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"); equivalent to `FORCE INDEX` with no modifier
        
        [`NO_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"): Same as [`NO_GROUP_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints") plus [`NO_JOIN_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints") plus [`NO_ORDER_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"); equivalent to `IGNORE INDEX` with no modifier
        
    
    For example, the following two queries are equivalent:
    
    ```sql
    SELECT a FROM t1 FORCE INDEX (i_a) FOR JOIN WHERE a=1 AND b=2;
    
    SELECT /*+ JOIN_INDEX(t1 i_a) */ a FROM t1 WHERE a=1 AND b=2;
    ```
    
    The optimizer hints listed previously follow the same basic rules for syntax and usage as existing index-level optimizer hints.
    
    These optimizer hints are intended to replace `FORCE INDEX` and `IGNORE INDEX`, which we plan to deprecate in a future MySQL release, and subsequently to remove from MySQL. They do not implement a single exact equivalent for `USE INDEX`; instead, you can employ one or more of [`NO_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"), [`NO_JOIN_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"), [`NO_GROUP_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints"), or [`NO_ORDER_INDEX`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints") to achieve the same effect.
    
    For further information and examples of use, see [Index-Level Optimizer Hints](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level "Index-Level Optimizer Hints").
    
- **JSON_VALUE() function. ** MySQL 8.0.21 implements a new function [`JSON_VALUE()`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#function_json-value) intended to simplify indexing of [`JSON`](https://dev.mysql.com/doc/refman/8.0/en/json.html "11.5 The JSON Data Type") columns. In its most basic form, it takes as arguments a JSON document and a JSON path pointing to a single value in that document, as well as (optionally) allowing you to specify a return type with the `RETURNING` keyword. ``JSON_VALUE(`json_doc`, `path` RETURNING `type`)`` is equivalent to this:
    
    ```sql
    CAST(
        JSON_UNQUOTE( JSON_EXTRACT(json_doc, path) )
        AS type
    );
    ```
    
    You can also specify `ON EMPTY`, `ON ERROR`, or both clauses, similar to those employed with [`JSON_TABLE()`](https://dev.mysql.com/doc/refman/8.0/en/json-table-functions.html#function_json-table).
    
    You can use `JSON_VALUE()` to create an index on an expression on a `JSON` column like this:
    
    ```sql
    CREATE TABLE t1(
        j JSON,
        INDEX i1 ( (JSON_VALUE(j, '$.id' RETURNING UNSIGNED)) )
    );
    
    INSERT INTO t1 VALUES ROW('{"id": "123", "name": "shoes", "price": "49.95"}');
    ```
    
    A query using this expression, such as that shown here, can make use of the index:
    
    ```sql
    SELECT name, price FROM t1
        WHERE JSON_VALUE(j, '$.id' RETURNING UNSIGNED) = 123;
    ```
    
    In many cases, this is simpler than creating a generated column from the `JSON` column and then creating an index on the generated column.
    
    For more information and examples, see the description of [`JSON_VALUE()`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#function_json-value).
    
- **User comments and user attributes. ** MySQL 8.0.21 introduces the ability to set user comments and user attributes when creating or updating user accounts. A user comment consists of arbitrary text passed as the argument to a `COMMENT` clause used with a [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html "13.7.1.3 CREATE USER Statement") or [`ALTER USER`](https://dev.mysql.com/doc/refman/8.0/en/alter-user.html "13.7.1.1 ALTER USER Statement") statement. A user attribute consists of data in the form of a JSON object passed as the argument to an `ATTRIBUTE` clause used with either of these two statements. The attribute can contain any valid key-value pairs in JSON object notation. Only one of `COMMENT` or `ATTRIBUTE` can be used in a single `CREATE USER` or `ALTER USER` statement.
    
    User comments and user attributes are stored together internally as a JSON object, the comment text as the value of an element having `comment` as its key. This information can be retrieved from the `ATTRIBUTE` column of the [`INFORMATION_SCHEMA.USER_ATTRIBUTES`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-user-attributes-table.html "26.3.46 The INFORMATION_SCHEMA USER_ATTRIBUTES Table") table; since it is in JSON format, you can use MySQL's JSON function and operators to parse its contents (see [Section 12.18, “JSON Functions”](https://dev.mysql.com/doc/refman/8.0/en/json-functions.html "12.18 JSON Functions")). Successive changes to the user attribute are merged with its current value as when using the [`JSON_MERGE_PATCH()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-merge-patch) function.
    
    Example:
    
    ```sql
    mysql> CREATE USER 'mary'@'localhost' COMMENT 'This is Mary Smith\'s account';
    Query OK, 0 rows affected (0.33 sec)
    
    mysql> ALTER USER 'mary'@'localhost'
        -≫     ATTRIBUTE '{"fname":"Mary", "lname":"Smith"}';
    Query OK, 0 rows affected (0.14 sec)
    
    mysql> ALTER USER 'mary'@'localhost'
        -≫     ATTRIBUTE '{"email":"mary.smith@example.com"}';
    Query OK, 0 rows affected (0.12 sec)
    
    mysql> SELECT
     ->    USER,
     ->    HOST,
     ->    ATTRIBUTE->>"$.fname" AS 'First Name',
     ->    ATTRIBUTE->>"$.lname" AS 'Last Name',
     ->    ATTRIBUTE->>"$.email" AS 'Email',
     ->    ATTRIBUTE->>"$.comment" AS 'Comment'
     -> FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
     -> WHERE USER='mary' AND HOST='localhost'\G
    *************************** 1. row ***************************
          USER: mary
          HOST: localhost
    First Name: Mary
     Last Name: Smith
         Email: mary.smith@example.com
       Comment: This is Mary Smith's account 1 row in set (0.00 sec)
    ```
    
    For more information and examples, see [Section 13.7.1.3, “CREATE USER Statement”](https://dev.mysql.com/doc/refman/8.0/en/create-user.html "13.7.1.3 CREATE USER Statement"), [Section 13.7.1.1, “ALTER USER Statement”](https://dev.mysql.com/doc/refman/8.0/en/alter-user.html "13.7.1.1 ALTER USER Statement"), and [Section 26.3.46, “The INFORMATION\_SCHEMA USER\_ATTRIBUTES Table”](https://dev.mysql.com/doc/refman/8.0/en/information-schema-user-attributes-table.html "26.3.46 The INFORMATION_SCHEMA USER_ATTRIBUTES Table").
    
- **New optimizer_switch flags. ** MySQL 8.0.21 adds two new flags for the [`optimizer_switch`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_optimizer_switch) system variable, as described in the following list:
    
    - [`prefer_ordering_index`](https://dev.mysql.com/doc/refman/8.0/en/switchable-optimizations.html#optflag_prefer-ordering-index) flag
        
        By default, MySQL attempts to use an ordered index for any `ORDER BY` or `GROUP BY` query that has a `LIMIT` clause, whenever the optimizer determines that this would result in faster execution. Because it is possible in some cases that choosing a different optimization for such queries actually performs better, it is now possible to disable this optimization by setting the [`prefer_ordering_index`](https://dev.mysql.com/doc/refman/8.0/en/switchable-optimizations.html#optflag_prefer-ordering-index) flag to `off`.
        
        The default value for this flag is `on`.
        
    - [`subquery_to_derived`](https://dev.mysql.com/doc/refman/8.0/en/switchable-optimizations.html#optflag_subquery-to-derived) flag
        
        When this flag is set to `on`, the optimizer transforms eligible scalar subqueries into joins on derived tables. For example, the query `SELECT * FROM t1 WHERE t1.a > (SELECT COUNT(a) FROM t2)` is rewritten as `SELECT t1.a FROM t1 JOIN ( SELECT COUNT(t2.a) AS c FROM t2 ) AS d WHERE t1.a > d.c`.
        
        This optimization can be applied to a subquery which is part of a `SELECT`, `WHERE`, `JOIN`, or `HAVING` clause; contains one or more aggregate functions but no `GROUP BY` clause; is not correlated; and does not use any nondeterministic functions.
        
        The optimization can also be applied to a table subquery which is the argument to `IN`, `NOT IN`, `EXISTS`, or `NOT EXISTS`, and which does not contain a `GROUP BY`. For example, the query `SELECT * FROM t1 WHERE t1.b < 0 OR t1.a IN (SELECT t2.a + 1 FROM t2)` is rewritten as `SELECT a, b FROM t1 LEFT JOIN (SELECT DISTINCT 1 AS e1, t2.a AS e2 FROM t2) d ON t1.a + 1 = d.e2 WHERE t1.b < 0 OR d.e1 IS NOT NULL`.
        
        Starting with MySQL 8.0.24, this optimization can also be applied to a correlated scalar subquery by applying an extra grouping to it, and then an outer join on the lifted predicate. For example, a query such as `SELECT * FROM t1 WHERE (SELECT a FROM t2 WHERE t2.a=t1.a) > 0` can be rewritten as `SELECT t1.* FROM t1 LEFT OUTER JOIN (SELECT a, COUNT(*) AS ct FROM t2 GROUP BY a) AS derived ON t1.a = derived.a WHERE derived.a > 0`. MySQL performs a cardinality check to make sure that the subquery does not return more than one row ([`ER_SUBQUERY_NO_1_ROW`](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html#error_er_subquery_no_1_row)). See [Section 13.2.11.7, “Correlated Subqueries”](https://dev.mysql.com/doc/refman/8.0/en/correlated-subqueries.html "13.2.11.7 Correlated Subqueries"), for more information.
        
        This optimization is normally disabled, since it does not yield a noticeable performance benefit in most cases; the flag is set to `off` by default.
        
    
    For more information, see [Section 8.9.2, “Switchable Optimizations”](https://dev.mysql.com/doc/refman/8.0/en/switchable-optimizations.html "8.9.2 Switchable Optimizations"). See also [Section 8.2.1.19, “LIMIT Query Optimization”](https://dev.mysql.com/doc/refman/8.0/en/limit-optimization.html "8.2.1.19 LIMIT Query Optimization"), [Section 8.2.2.1, “Optimizing IN and EXISTS Subquery Predicates with Semijoin Transformations”](https://dev.mysql.com/doc/refman/8.0/en/semijoins.html "8.2.2.1 Optimizing IN and EXISTS Subquery Predicates with Semijoin Transformations"), and [Section 8.2.2.4, “Optimizing Derived Tables, View References, and Common Table Expressions with Merging or Materialization”](https://dev.mysql.com/doc/refman/8.0/en/derived-table-optimization.html "8.2.2.4 Optimizing Derived Tables, View References, and Common Table Expressions with Merging or Materialization").
    
- **XML enhancements. ** As of MySQL 8.0.21, the [`LOAD XML`](https://dev.mysql.com/doc/refman/8.0/en/load-xml.html "13.2.8 LOAD XML Statement") statement now supports `CDATA` sections in the XML to be imported.
    
- **Casting to the YEAR type now supported. ** Beginning with MySQL 8.0.22, the server allows casting to [`YEAR`](https://dev.mysql.com/doc/refman/8.0/en/year.html "11.2.4 The YEAR Type"). Both the [`CAST()`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#function_cast) and [`CONVERT()`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#function_convert) functions support single-digit, two-digit, and four-digit `YEAR` values. For one-digit and two-digit values, the allowed range is 0-99. Four-digit values must be in the range 1901-2155. `YEAR` can also be used as the return type for the [`JSON_VALUE()`](https://dev.mysql.com/doc/refman/8.0/en/json-search-functions.html#function_json-value) function; this function supports four-digit years only.
    
    String, time-and-date, and floating-point values can all be cast to `YEAR`. Casting of [`GEOMETRY`](https://dev.mysql.com/doc/refman/8.0/en/spatial-type-overview.html "11.4.1 Spatial Data Types") values to `YEAR` is not supported.
    
    For more information, including conversion rules, see the description of the [`CONVERT()`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#function_convert) function.
    
- **Retrieval of TIMESTAMP values as UTC. ** MySQL 8.0.22 and later supports conversion of a [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types") column value from the system time zone to a UTC [`DATETIME`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types") on retrieval, using ``CAST(`value` AT TIME ZONE `specifier` AS DATETIME)``, where the specifier is one of `[INTERVAL] '+00:00'` or `'UTC'`. The precision of the `DATETIME` value returned by the cast can be specified up to 6 decimal places, if desired. The `ARRAY` keyword is not supported with this construct.
    
    `TIMESTAMP` values inserted into a table using a timezone offset are also supported. Use of `AT TIME ZONE` is not supported for [`CONVERT()`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#function_convert) or any other MySQL function or construct.
    
    For further information and examples, see the description of the [`CAST()`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#function_cast) function.
    
- **Dump file output synchronization. ** MySQL 8.0.22 and later supports periodic synchronization when writing to files by [`SELECT INTO DUMPFILE`](https://dev.mysql.com/doc/refman/8.0/en/select-into.html "13.2.10.1 SELECT ... INTO Statement") and `SELECT INTO OUTFILE` statements. This can be enabled by setting the [`select_into_disk_sync`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_select_into_disk_sync) system variable to `ON`; the size of the write buffer is determined by the value set for [`select_into_buffer_size`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_select_into_buffer_size); the default is 131072 (2<sup>17</sup>) bytes.
    
    In addition, an optional delay following synchronization to disk can be set using [`select_into_disk_sync_delay`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_select_into_disk_sync_delay); the default is no delay (0 milliseconds).
    
    For more information, see the descriptions of the variables referenced previously in this item.
    
- **Single preparation of statements. ** As of MySQL 8.0.22, a prepared statement is prepared a single time, rather than once each time it is executed. This is done when executing [`PREPARE`](https://dev.mysql.com/doc/refman/8.0/en/prepare.html "13.5.1 PREPARE Statement"). This is also true for any statement inside a stored procedure; the statement is prepared once, when the stored procedure is first executed.
    
    One result of this change is that the fashion in which dynamic parameters used in prepared statements are resolved is also changed in the ways listed here:
    
    - A prepared statement parameter is assigned a data type when the statement is prepared; the type persists for each subsequent execution of the statement (unless the statement is reprepared; see following).
        
        Using a different data type for a given parameter or user variable within a prepared statement for executions of the statement subsequent to the first execution may cause the statement to be reprepared; for this reason, it is advisable to use the same data type for a given parameter when re-executing a prepared statement.
        
    - The following constructs employing window functions are no longer accepted, in order to align with the SQL standard:
        
        - [`NTILE(NULL)`](https://dev.mysql.com/doc/refman/8.0/en/window-function-descriptions.html#function_ntile)
            
        - [``NTH_VALUE(`expr`, NULL)``](https://dev.mysql.com/doc/refman/8.0/en/window-function-descriptions.html#function_nth-value)
            
        - [``LEAD(`expr`, `nn`)``](https://dev.mysql.com/doc/refman/8.0/en/window-function-descriptions.html#function_lead) and [``LAG(`expr`, `nn`)``](https://dev.mysql.com/doc/refman/8.0/en/window-function-descriptions.html#function_lag), where *`nn`* is a negative number
            
        
        This facilitates greater compliance with the SQL standard. See the individual function descriptions for further details.
        
    - A user variable referenced within a prepared statement now has its data type determined when the statement is prepared; the type persists for each subsequent execution of the statement.
        
    - A user variable referenced by a statement occurring within a stored procedure now has its data type determined the first time the statement is executed; the type persists for any subsequent invocation of the containing stored procedure.
        
    - When executing a prepared statement of the form ``SELECT `expr1`, `expr2`, ... FROM `table` ORDER BY ?``, passing an integer value *`N`* for the parameter no longer causes ordering of the results by the *`N`*<sup>th</sup> expression in the select list; the results are no longer ordered, as is expected with ``ORDER BY `constant` ``.
        
    
    Preparing a statement used as a prepared statement or within a stored procedure only once enhances the performance of the statement, since it negates the added cost of repeated preparation. Doing so also avoids possible multiple rollbacks of preparation structures, which has been the source of numerous issues in MySQL.
    
    For more information, see [Section 13.5.1, “PREPARE Statement”](https://dev.mysql.com/doc/refman/8.0/en/prepare.html "13.5.1 PREPARE Statement").
    
- **RIGHT JOIN as LEFT JOIN handling. ** As of MySQL 8.0.22, the server handles all instances of `RIGHT JOIN` internally as `LEFT JOIN`, eliminating a number of special cases in which a complete conversion was not performed at parse time.
    
- **Derived condition pushdown optimization. ** MySQL 8.0.22 (and later) implements derived condition pushdown for queries having materialized derived tables. For a query such as ``SELECT * FROM (SELECT i, j FROM t1) AS dt WHERE i > `constant` ``, it is now possible in many cases to push the the outer `WHERE` condition down to the derived table, in this case resulting in ``SELECT * FROM (SELECT i, j FROM t1 WHERE i > `constant`) AS dt``.
    
    Previously, if the derived table was materialized and not merged, MySQL materialized the entire table, then qualified the rows with the `WHERE` condition. Moving the `WHERE` condition into the subquery using the derived condition pushdown optimization can often reduce the number of rows must be be processed, which can decrease the time needed to execute the query.
    
    An outer `WHERE` condition can be pushed down directly to a materialized derived table when the derived table does not use any aggregate or window functions. When the derived table has a `GROUP BY` and does not use any window functions, the outer `WHERE` condition can be pushed down to the derived table as a `HAVING` condition. The `WHERE` condition can also be pushed down when the derived table uses a window function and the outer `WHERE` references columns used in the window function's `PARTITION` clause.
    
    Derived condition pushdown is enabled by default, as indicated by the [`optimizer_switch`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_optimizer_switch) system variable's [`derived_condition_pushdown`](https://dev.mysql.com/doc/refman/8.0/en/switchable-optimizations.html#optflag_derived-condition-pushdown) flag. The flag, added in MySQL 8.0.22, is set to `on` by default; to disable the optimization for a specific query, you can use the [`NO_DERIVED_CONDITION_PUSHDOWN`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level "Table-Level Optimizer Hints") optimizer hint (also added in MySQL 8.0.22). If the optimization is disabled due to [`derived_condition_pushdown`](https://dev.mysql.com/doc/refman/8.0/en/switchable-optimizations.html#optflag_derived-condition-pushdown) being set to `off`, you can enable it for a given query using [`DERIVED_CONDITION_PUSHDOWN`](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level "Table-Level Optimizer Hints").
    
    The derived condition pushdown optimization cannot be employed for a derived table that contains a [`UNION`](https://dev.mysql.com/doc/refman/8.0/en/union.html "13.2.10.3 UNION Clause") or `LIMIT` clause. In addition, a condition that itself uses a subquery cannot be pushed down, and a `WHERE` condition cannot be pushed down to a derived table that is also an inner table of an outer join. For additional information and examples, see [Section 8.2.2.5, “Derived Condition Pushdown Optimization”](https://dev.mysql.com/doc/refman/8.0/en/derived-condition-pushdown-optimization.html "8.2.2.5 Derived Condition Pushdown Optimization").
    
- **Non-locking reads on MySQL grant tables. ** As of MySQL 8.0.22, to permit concurrent DML and DDL operations on MySQL grant tables, read operations that previously acquired row locks on MySQL grant tables are executed as non-locking reads.
    
    The operations that are now performed as non-locking reads on MySQL grant tables include:
    
    - [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") statements and other read-only statements that read data from grant tables through join lists and subqueries, including [`SELECT ... FOR SHARE`](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-reads.html "15.7.2.4 Locking Reads") statements, using any transaction isolation level.
        
    - DML operations that read data from grant tables (through join lists or subqueries) but do not modify them, using any transaction isolation level.
        
    
    For additional information, see [Grant Table Concurrency](https://dev.mysql.com/doc/refman/8.0/en/grant-tables.html#grant-tables-concurrency "Grant Table Concurrency").
    
- **64-bit support for FROM\_UNIXTIME(), UNIX\_TIMESTAMP(), CONVERT_TZ(). ** As of MySQL 8.0.28, the functions [`FROM_UNIXTIME()`](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_from-unixtime), [`UNIX_TIMESTAMP()`](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_unix-timestamp), and [`CONVERT_TZ()`](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_convert-tz) handle 64-bit values on platforms that support them. This includes 64-bit versions of Linux, MacOS, and Windows.
    
    On compatible platforms, `UNIX_TIMESTAMP()` now handles values up to `'3001-01-18 23:59:59.999999'` UTC, and `FROM_UNIXTIME()` can convert values up to 32536771199.999999 seconds since the Unix Epoch; `CONVERT_TZ()` now accepts values that do not exceed `'3001-01-18 23:59:59.999999'` UTC following conversion.
    
    The behavior of these functions on 32-bit platforms is unaffected by these changes. The behavior of the [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types") type is also not affected (on any platform); for working with datetimes after `'2038-01-19 03:14:07.999999'`, UTC, use the [`DATETIME`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types") type instead.
    
    For more information, see the descriptions of the individual functions just discussed, in [Section 12.7, “Date and Time Functions”](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html "12.7 Date and Time Functions").
    
- **Resource allocation control. ** Beginning with MySQL 8.0.28, you can see the amount of memory used for queries issued by all regular users by checking the [`Global_connection_memory`](https://dev.mysql.com/doc/refman/8.0/en/server-status-variables.html#statvar_Global_connection_memory) status variable. (This total does not include resources used by system users such as MySQL root. It is also exclusive of any memory taken by the [`InnoDB`](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html "Chapter 15 The InnoDB Storage Engine") buffer pool.)
    
    To enable updates of `Global_connection_memory`, it is necessary to set [`global_connection_memory_tracking = 1`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_global_connection_memory_tracking); this is `0` (off) by default. You can control how often `Global_connection_memory` is updated by setting [`connection_memory_chunk_size`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_connection_memory_chunk_size).
    
    It is also possible to set memory usage limits for normal users on the session or global level, or both, by setting either or both of the system variables listed here:
    
    - [`connection_memory_limit`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_connection_memory_limit): Amount of memory allocated for each connection. Whenever this limit is exceeded for any user, new queries from this user are rejected.
        
    - [`global_connection_memory_limit`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_global_connection_memory_limit): Amount of memory allocated for all connections. Whenever this limit is exceeded, new queries from any regular user are rejected.
        
    
    These limits do not apply to system processes or administrative accounts.
    
    See the descriptions of the referenced variables for more information.
    

### <a id="mysql-nutshell-deprecations"></a>Features Deprecated in MySQL 8.0

<a id="idm45429586350272"></a><a id="idm45429586349200"></a>

The following features are deprecated in MySQL 8.0 and may be removed in a future series. Where alternatives are shown, applications should be updated to use them.

For applications that use features deprecated in MySQL 8.0 that have been removed in a higher MySQL series, statements may fail when replicated from a MySQL 8.0 source to a higher-series replica, or may have different effects on source and replica. To avoid such problems, applications that use features deprecated in 8.0 should be revised to avoid them and use alternatives when possible.

- The `utf8mb3` character set is deprecated. Please use `utf8mb4` instead.
    
    <a id="idm45429586343856"></a><a id="idm45429586342368"></a>
- The following character sets are deprecated:
    
    <a id="idm45429586340032"></a><a id="idm45429586338544"></a><a id="idm45429586337056"></a><a id="idm45429586335568"></a><a id="idm45429586334080"></a><a id="idm45429586332592"></a><a id="idm45429586331104"></a><a id="idm45429586329600"></a><a id="idm45429586328144"></a><a id="idm45429586326640"></a>
    
    - `ucs2` (see [Section 10.9.4, “The ucs2 Character Set (UCS-2 Unicode Encoding)”](https://dev.mysql.com/doc/refman/8.0/en/charset-unicode-ucs2.html "10.9.4 The ucs2 Character Set (UCS-2 Unicode Encoding)"))
        
    - `macroman` and `macce` (see [Section 10.10.2, “West European Character Sets”](https://dev.mysql.com/doc/refman/8.0/en/charset-we-sets.html "10.10.2 West European Character Sets"), and [Section 10.10.3, “Central European Character Sets”](https://dev.mysql.com/doc/refman/8.0/en/charset-ce-sets.html "10.10.3 Central European Character Sets"))
        
    - `dec` (see [Section 10.10.2, “West European Character Sets”](https://dev.mysql.com/doc/refman/8.0/en/charset-we-sets.html "10.10.2 West European Character Sets"))
        
    - `hp8` (see [Section 10.10.2, “West European Character Sets”](https://dev.mysql.com/doc/refman/8.0/en/charset-we-sets.html "10.10.2 West European Character Sets"))
        
    
    In MySQL 8.0.28 and later, any of these character sets or their collations produces a deprecation warning when used in either of the following ways:
    
    - When starting the MySQL server with [`--character-set-server`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_character_set_server) or [`--collation-server`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_collation_server)
        
    - When specified in any SQL statement, including but not limited to [`CREATE TABLE`](https://dev.mysql.com/doc/refman/8.0/en/create-table.html "13.1.20 CREATE TABLE Statement"), [`CREATE DATABASE`](https://dev.mysql.com/doc/refman/8.0/en/create-database.html "13.1.12 CREATE DATABASE Statement"), [`SET NAMES`](https://dev.mysql.com/doc/refman/8.0/en/set-names.html "13.7.6.3 SET NAMES Statement"), and [`ALTER TABLE`](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html "13.1.9 ALTER TABLE Statement")
        
    
    You should use `utf8mb4` instead any of the character sets listed previously.
    
- Because `caching_sha2_password` is the default authentication plugin in MySQL 8.0 and provides a superset of the capabilities of the `sha256_password` authentication plugin, `sha256_password` is deprecated; expect it to be removed in a future version of MySQL. MySQL accounts that authenticate using `sha256_password` should be migrated to use `caching_sha2_password` instead.
    
    <a id="idm45429586298320"></a><a id="idm45429586296832"></a>
- The `validate_password` plugin has been reimplemented to use the component infrastructure. The plugin form of `validate_password` is still available but is now deprecated; expect it to be removed in a future version of MySQL. MySQL installations that use the plugin should make the transition to using the component instead. See [Section 6.4.3.3, “Transitioning to the Password Validation Component”](https://dev.mysql.com/doc/refman/8.0/en/validate-password-transitioning.html "6.4.3.3 Transitioning to the Password Validation Component").
    
    <a id="idm45429586291840"></a><a id="idm45429586290384"></a>
- The `ENGINE` clause for the [`ALTER TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/alter-tablespace.html "13.1.10 ALTER TABLESPACE Statement") and [`DROP TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/drop-tablespace.html "13.1.33 DROP TABLESPACE Statement") statements is deprecated.
    
    <a id="idm45429586284848"></a><a id="idm45429586283360"></a>
- The [`PAD_CHAR_TO_FULL_LENGTH`](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sqlmode_pad_char_to_full_length) SQL mode is deprecated.
    
    <a id="idm45429586279600"></a><a id="idm45429586278112"></a>
- `AUTO_INCREMENT` support is deprecated for columns of type [`FLOAT`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE") and [`DOUBLE`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE") (and any synonyms). Consider removing the `AUTO_INCREMENT` attribute from such columns, or convert them to an integer type.
    
    <a id="idm45429586271856"></a><a id="idm45429586270368"></a>
- The `UNSIGNED` attribute is deprecated for columns of type [`FLOAT`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE"), [`DOUBLE`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE"), and [`DECIMAL`](https://dev.mysql.com/doc/refman/8.0/en/fixed-point-types.html "11.1.3 Fixed-Point Types (Exact Value) - DECIMAL, NUMERIC") (and any synonyms). Consider using a simple `CHECK` constraint instead for such columns.
    
    <a id="idm45429586262800"></a><a id="idm45429586261312"></a>
- ``FLOAT(`M`,`D`)`` and ``DOUBLE(`M`,`D`)`` syntax to specify the number of digits for columns of type [`FLOAT`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE") and [`DOUBLE`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html "11.1.4 Floating-Point Types (Approximate Value) - FLOAT, DOUBLE") (and any synonyms) is a nonstandard MySQL extension. This syntax is deprecated.
    
    <a id="idm45429586253456"></a><a id="idm45429586251968"></a>
- The `ZEROFILL` attribute is deprecated for numeric data types, as is the display width attribute for integer data types. Consider using an alternative means of producing the effect of these attributes. For example, applications could use the [`LPAD()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_lpad) function to zero-pad numbers up to the desired width, or they could store the formatted numbers in [`CHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html "11.3.2 The CHAR and VARCHAR Types") columns.
    
- For string data types, the `BINARY` attribute is a nonstandard MySQL extension that is shorthand for specifying the binary (`_bin`) collation of the column character set (or of the table default character set if no column character set is specified). In MySQL 8.0, this nonstandard use of `BINARY` is ambiguous because the `utf8mb4` character set has multiple `_bin` collations, so the `BINARY` attribute is deprecated; expect support for it to be removed in a future version of MySQL. Applications should be adjusted to use an explicit `_bin` collation instead.
    
    <a id="idm45429586239568"></a><a id="idm45429586238080"></a>
    
    The use of `BINARY` to specify a data type or character set remains unchanged.
    
- Previous versions of MySQL supported the nonstandard shorthand expressions `ASCII` and `UNICODE`, respectively, for `CHARACTER SET latin1` and `CHARACTER SET ucs2`. `ASCII` and `UNICODE` are deprecated (MySQL 8.0.28 and later) and now produce a warning. Use `CHARACTER SET` instead, in both cases.
    
    <a id="idm45429586229328"></a><a id="idm45429586227840"></a><a id="idm45429586226352"></a><a id="idm45429586224864"></a>
- The nonstandard C-style [`&&`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_and), [`||`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_or), and [`!`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_not) operators that are synonyms for the standard SQL [`AND`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_and), [`OR`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_or), and [`NOT`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_not) operators, respectively, are deprecated. Applications that use the nonstandard operators should be adjusted to use the standard operators.
    
    <a id="idm45429586214432"></a><a id="idm45429586212944"></a><a id="idm45429586211456"></a><a id="idm45429586210000"></a><a id="idm45429586208544"></a><a id="idm45429586207088"></a>
    
    Note
    
    Use of [`||`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_or) is deprecated unless the [`PIPES_AS_CONCAT`](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sqlmode_pipes_as_concat) SQL mode is enabled. In that case, `||` signifies the SQL-standard string concatenation operator).
    
- The [`JSON_MERGE()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-merge) function is deprecated. Use [`JSON_MERGE_PRESERVE()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-merge-preserve) instead.
    
    <a id="idm45429586198128"></a><a id="idm45429586196640"></a>
- The `SQL_CALC_FOUND_ROWS` query modifier and accompanying [`FOUND_ROWS()`](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_found-rows) function are deprecated. See the [`FOUND_ROWS()`](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_found-rows) description for information about an alternative strategy.
    
    <a id="idm45429586190848"></a><a id="idm45429586189360"></a><a id="idm45429586187872"></a><a id="idm45429586186384"></a>
- Support for `TABLESPACE = innodb_file_per_table` and `TABLESPACE = innodb_temporary` clauses with [`CREATE TEMPORARY TABLE`](https://dev.mysql.com/doc/refman/8.0/en/create-table.html "13.1.20 CREATE TABLE Statement") is deprecated as of MySQL 8.0.13.
    
    <a id="idm45429586181280"></a><a id="idm45429586179792"></a>
- For [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") statements, use of an `INTO` clause after `FROM` but not at the end of the [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") is deprecated as of MySQL 8.0.20. It is preferred to place the `INTO` at the end of the statement.
    
    For [`UNION`](https://dev.mysql.com/doc/refman/8.0/en/union.html "13.2.10.3 UNION Clause") statements, these two variants containing `INTO` are deprecated as of MySQL 8.0.20:
    
    - In the trailing query block of a query expression, use of `INTO` before `FROM`.
        
    - In a parenthesized trailing block of a query expression, use of `INTO`, regardless of its position relative to `FROM`.
        
    
    See [Section 13.2.10.1, “SELECT ... INTO Statement”](https://dev.mysql.com/doc/refman/8.0/en/select-into.html "13.2.10.1 SELECT ... INTO Statement"), and [Section 13.2.10.3, “UNION Clause”](https://dev.mysql.com/doc/refman/8.0/en/union.html "13.2.10.3 UNION Clause").
    
    <a id="idm45429586163424"></a><a id="idm45429586161936"></a><a id="idm45429586160448"></a><a id="idm45429586158960"></a>
- [`FLUSH HOSTS`](https://dev.mysql.com/doc/refman/8.0/en/flush.html#flush-hosts) is deprecated as of MySQL 8.0.23. Instead, truncate the Performance Schema [`host_cache`](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-host-cache-table.html "27.12.21.2 The host_cache Table") table:
    
    ```sql
    TRUNCATE TABLE performance_schema.host_cache;
    ```
    
    The [`TRUNCATE TABLE`](https://dev.mysql.com/doc/refman/8.0/en/truncate-table.html "13.1.37 TRUNCATE TABLE Statement") operation requires the [`DROP`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_drop) privilege for the table.
    
- The [**mysql_upgrade**](https://dev.mysql.com/doc/refman/8.0/en/mysql-upgrade.html "4.4.5 mysql_upgrade — Check and Upgrade MySQL Tables") client is deprecated because its capabilities for upgrading the system tables in the `mysql` system schema and objects in other schemas have been moved into the MySQL server. See [Section 2.11.3, “What the MySQL Upgrade Process Upgrades”](https://dev.mysql.com/doc/refman/8.0/en/upgrading-what-is-upgraded.html "2.11.3 What the MySQL Upgrade Process Upgrades").
    
    <a id="idm45429586146128"></a><a id="idm45429586144640"></a>
- The [`--no-dd-upgrade`](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_no-dd-upgrade) server option is deprecated. It is superseded by the [`--upgrade`](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_upgrade) option, which provides finer control over data dictionary and server upgrade behavior.
    
    <a id="idm45429586140144"></a><a id="idm45429586138656"></a>
- The `mysql_upgrade_info` file, which is created data directory and used to store the MySQL version number, is deprecated; expect it to be removed in a future version of MySQL.
    
    <a id="idm45429586135472"></a><a id="idm45429586133984"></a>
- The `relay_log_info_file` system variable and `--master-info-file` option are deprecated. Previously, these were used to specify the name of the relay log info log and source info log when [`relay_log_info_repository=FILE`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#sysvar_relay_log_info_repository) and [`master_info_repository=FILE`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#sysvar_master_info_repository) were set, but those settings have been deprecated. The use of files for the relay log info log and source info log has been superseded by crash-safe replica tables, which are the default in MySQL 8.0.
    
    <a id="idm45429586127200"></a><a id="idm45429586125712"></a><a id="idm45429586124224"></a><a id="idm45429586122736"></a>
- The [`max_length_for_sort_data`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_length_for_sort_data) system variable is now deprecated due to optimizer changes that make it obsolete and of no effect.
    
    <a id="idm45429586118864"></a><a id="idm45429586117360"></a>
- These legacy parameters for compression of connections to the server are deprecated: The [`--compress`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_compress) client command-line option; the `MYSQL_OPT_COMPRESS` option for the [`mysql_options()`](https://dev.mysql.com/doc/c-api/8.0/en/mysql-options.html) C API function; the [`slave_compressed_protocol`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#sysvar_slave_compressed_protocol) system variable. For information about parameters to use instead, see [Section 4.2.8, “Connection Compression Control”](https://dev.mysql.com/doc/refman/8.0/en/connection-compression-control.html "4.2.8 Connection Compression Control").
    
    <a id="idm45429586109584"></a><a id="idm45429586108096"></a><a id="idm45429586106608"></a><a id="idm45429586105120"></a><a id="idm45429586103632"></a><a id="idm45429586102176"></a>
- Use of the `MYSQL_PWD` environment variable to specify a MySQL password is deprecated.
    
    <a id="idm45429586099072"></a><a id="idm45429586097584"></a>
- Use of [`VALUES()`](https://dev.mysql.com/doc/refman/8.0/en/miscellaneous-functions.html#function_values) to access new row values in [`INSERT ... ON DUPLICATE KEY UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/insert-on-duplicate.html "13.2.6.2 INSERT ... ON DUPLICATE KEY UPDATE Statement") is deprecated as of MySQL 8.0.20. Use aliases for the new row and columns, instead.
    
    <a id="idm45429586092560"></a><a id="idm45429586091072"></a><a id="idm45429586089568"></a><a id="idm45429586088080"></a>
- Because specifying `ON ERROR` before `ON EMPTY` when invoking [`JSON_TABLE()`](https://dev.mysql.com/doc/refman/8.0/en/json-table-functions.html#function_json-table) is counter to the SQL standard, this syntax is now deprecated in MySQL. Beginning with MySQL 8.0.20, the server prints a warning whenever you attempt to do so. When specifying both of these clauses in a single `JSON_TABLE()` invocation, make sure that `ON EMPTY` is used first.
    
    <a id="idm45429586081344"></a><a id="idm45429586079856"></a>
- Columns with index prefixes have never been supported as part of a table's partitioning key; previously, these were allowed when creating, altering, or upgrading partitioned tables but were excluded by the table's partitioning function, and no warning that this had occurred was issued by the server. This permissive behavior is now deprecated, and subject to removal in a future version of MySQL in which using any such columns in the partitioning key causes the [`CREATE TABLE`](https://dev.mysql.com/doc/refman/8.0/en/create-table.html "13.1.20 CREATE TABLE Statement") or [`ALTER TABLE`](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html "13.1.9 ALTER TABLE Statement") statement in they occur to be rejected.
    
    As of MySQL 8.0.21, whenever columns using index prefixes are specified as part of the partitioning key, a warning is generated for each such column. Whenever a [`CREATE TABLE`](https://dev.mysql.com/doc/refman/8.0/en/create-table.html "13.1.20 CREATE TABLE Statement") or [`ALTER TABLE`](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html "13.1.9 ALTER TABLE Statement") statement is rejected because all columns in the proposed partitioning key would have index prefixes, the resulting error now provides the exact reason for the rejection. In either instance, this includes cases in which the columns used in the partitioning function are defined implicitly as those in the table's primary key by employing an empty `PARTITION BY KEY()` clause.
    
    For more information and examples, see [Column index prefixes not supported for key partitioning](https://dev.mysql.com/doc/refman/8.0/en/partitioning-limitations.html#partitioning-limitations-prefixes "Column index prefixes not supported for key partitioning").
    
    <a id="idm45429586068896"></a><a id="idm45429586067376"></a>
- The InnoDB memcached plugin is deprecated as of MySQL 8.0.22; expect support for it to be removed in a future version of MySQL.
    
    <a id="idm45429586064896"></a><a id="idm45429586063408"></a>
- The [`temptable_use_mmap`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_temptable_use_mmap) variable is deprecated as of MySQL 8.0.26; expect support for it to be removed in a future version of MySQL.
    
    <a id="idm45429586059728"></a><a id="idm45429586058240"></a>
- The [`BINARY`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#operator_binary) operator is deprecated as of MySQL 8.0.27, and you should expect its removal in a future version of MySQL. Use of `BINARY` now causes a warning. Use [`CAST(... AS BINARY)`](https://dev.mysql.com/doc/refman/8.0/en/cast-functions.html#function_cast) instead.
    
    <a id="idm45429586052544"></a><a id="idm45429586051056"></a>
- The [`default_authentication_plugin`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin) variable is deprecated as of MySQL 8.0.27; expect support for it to be removed in a future version of MySQL.
    
    The [`default_authentication_plugin`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin) variable is still used in MySQL 8.0.27, but in conjunction with and at a lower precedence than the new `authentication_policy` system variable, which is introduced in MySQL 8.0.27 with the multifactor authentication feature. For details, see [The Default Authentication Plugin](https://dev.mysql.com/doc/refman/8.0/en/pluggable-authentication.html#pluggable-authentication-default-plugin "The Default Authentication Plugin").
    
    <a id="idm45429586043584"></a><a id="idm45429586042128"></a>

### <a id="mysql-nutshell-removals"></a>Features Removed in MySQL 8.0

<a id="idm45429586039152"></a><a id="idm45429586038112"></a>

The following items are obsolete and have been removed in MySQL 8.0. Where alternatives are shown, applications should be updated to use them.

For MySQL 5.7 applications that use features removed in MySQL 8.0, statements may fail when replicated from a MySQL 5.7 source to a MySQL 8.0 replica, or may have different effects on source and replica. To avoid such problems, applications that use features removed in MySQL 8.0 should be revised to avoid them and use alternatives when possible.

- The `innodb_locks_unsafe_for_binlog` system variable was removed. The [`READ COMMITTED`](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html#isolevel_read-committed) isolation level provides similar functionality.
    
    <a id="idm45429586032064"></a><a id="idm45429586030560"></a>
- The `information_schema_stats` variable, introduced in MySQL 8.0.0, was removed and replaced by [`information_schema_stats_expiry`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_information_schema_stats_expiry) in MySQL 8.0.3.
    
    <a id="idm45429586026080"></a><a id="idm45429586024576"></a>
    
    `information_schema_stats_expiry` defines an expiration setting for cached [`INFORMATION_SCHEMA`](https://dev.mysql.com/doc/refman/8.0/en/information-schema.html "Chapter 26 INFORMATION_SCHEMA Tables") table statistics. For more information, see [Section 8.2.3, “Optimizing INFORMATION_SCHEMA Queries”](https://dev.mysql.com/doc/refman/8.0/en/information-schema-optimization.html "8.2.3 Optimizing INFORMATION_SCHEMA Queries").
    
- Code related to obsoleted `InnoDB` system tables was removed in MySQL 8.0.3. [`INFORMATION_SCHEMA`](https://dev.mysql.com/doc/refman/8.0/en/information-schema.html "Chapter 26 INFORMATION_SCHEMA Tables") views based on `InnoDB` system tables were replaced by internal system views on data dictionary tables. Affected `InnoDB` [`INFORMATION_SCHEMA`](https://dev.mysql.com/doc/refman/8.0/en/information-schema.html "Chapter 26 INFORMATION_SCHEMA Tables") views were renamed:
    
    <a id="innodb-new-is-views"></a>
    
    **Table 1.1 Renamed InnoDB Information Schema Views**
    
    | Old Name | New Name |
    | --- | --- |
    | `INNODB_SYS_COLUMNS` | `INNODB_COLUMNS` |
    | `INNODB_SYS_DATAFILES` | `INNODB_DATAFILES` |
    | `INNODB_SYS_FIELDS` | `INNODB_FIELDS` |
    | `INNODB_SYS_FOREIGN` | `INNODB_FOREIGN` |
    | `INNODB_SYS_FOREIGN_COLS` | `INNODB_FOREIGN_COLS` |
    | `INNODB_SYS_INDEXES` | `INNODB_INDEXES` |
    | `INNODB_SYS_TABLES` | `INNODB_TABLES` |
    | `INNODB_SYS_TABLESPACES` | `INNODB_TABLESPACES` |
    | `INNODB_SYS_TABLESTATS` | `INNODB_TABLESTATS` |
    | `INNODB_SYS_VIRTUAL` | `INNODB_VIRTUAL` |
    
    <a id="idm45429585978656"></a><a id="idm45429585977168"></a><a id="idm45429585975680"></a><a id="idm45429585974192"></a><a id="idm45429585972704"></a><a id="idm45429585971216"></a><a id="idm45429585969728"></a><a id="idm45429585968240"></a><a id="idm45429585966752"></a><a id="idm45429585965264"></a><a id="idm45429585963776"></a><a id="idm45429585962288"></a><a id="idm45429585960800"></a><a id="idm45429585959312"></a><a id="idm45429585957824"></a><a id="idm45429585956336"></a><a id="idm45429585954848"></a><a id="idm45429585953360"></a><a id="idm45429585951872"></a><a id="idm45429585950384"></a>
    
    After upgrading to MySQL 8.0.3 or later, update any scripts that reference previous `InnoDB` [`INFORMATION_SCHEMA`](https://dev.mysql.com/doc/refman/8.0/en/information-schema.html "Chapter 26 INFORMATION_SCHEMA Tables") view names.
    
- The following features related to account management are removed:
    
    - Using [`GRANT`](https://dev.mysql.com/doc/refman/8.0/en/grant.html "13.7.1.6 GRANT Statement") to create users. Instead, use [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html "13.7.1.3 CREATE USER Statement"). Following this practice makes the `NO_AUTO_CREATE_USER` SQL mode immaterial for [`GRANT`](https://dev.mysql.com/doc/refman/8.0/en/grant.html "13.7.1.6 GRANT Statement") statements, so it too is removed, and an error now is written to the server log when the presence of this value for the `sql_mode` option in the options file prevents [**mysqld**](https://dev.mysql.com/doc/refman/8.0/en/mysqld.html "4.3.1 mysqld — The MySQL Server") from starting.
        
        <a id="idm45429585937952"></a><a id="idm45429585936464"></a>
    - Using [`GRANT`](https://dev.mysql.com/doc/refman/8.0/en/grant.html "13.7.1.6 GRANT Statement") to modify account properties other than privilege assignments. This includes authentication, SSL, and resource-limit properties. Instead, establish such properties at account-creation time with [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html "13.7.1.3 CREATE USER Statement") or modify them afterward with [`ALTER USER`](https://dev.mysql.com/doc/refman/8.0/en/alter-user.html "13.7.1.1 ALTER USER Statement").
        
    - ``IDENTIFIED BY PASSWORD '`auth_string`'`` syntax for [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html "13.7.1.3 CREATE USER Statement") and [`GRANT`](https://dev.mysql.com/doc/refman/8.0/en/grant.html "13.7.1.6 GRANT Statement"). Instead, use ``IDENTIFIED WITH `auth_plugin` AS '`auth_string`'`` for [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/create-user.html "13.7.1.3 CREATE USER Statement") and [`ALTER USER`](https://dev.mysql.com/doc/refman/8.0/en/alter-user.html "13.7.1.1 ALTER USER Statement"), where the ``'`auth_string`'`` value is in a format compatible with the named plugin.
        
        <a id="idm45429585920512"></a><a id="idm45429585919024"></a>
        
        Additionally, because `IDENTIFIED BY PASSWORD` syntax was removed, the `log_builtin_as_identified_by_password` system variable is superfluous and was removed.
        
        <a id="idm45429585915536"></a><a id="idm45429585914032"></a>
    - The `PASSWORD()` function. Additionally, `PASSWORD()` removal means that [``SET PASSWORD ... = PASSWORD('`auth_string`')``](https://dev.mysql.com/doc/refman/8.0/en/set-password.html "13.7.1.10 SET PASSWORD Statement") syntax is no longer available.
        
        <a id="idm45429585908256"></a><a id="idm45429585906768"></a>
    - The `old_passwords` system variable.
        
        <a id="idm45429585903744"></a><a id="idm45429585902256"></a>
    
- The query cache was removed. Removal includes these items:
    
    <a id="idm45429585899648"></a><a id="idm45429585898160"></a>
    
    - The `FLUSH QUERY CACHE` and `RESET QUERY CACHE` statements.
        
        <a id="idm45429585894288"></a><a id="idm45429585892800"></a><a id="idm45429585891312"></a><a id="idm45429585889824"></a>
    - These system variables: `query_cache_limit`, `query_cache_min_res_unit`, `query_cache_size`, `query_cache_type`, `query_cache_wlock_invalidate`.
        
        <a id="idm45429585883856"></a><a id="idm45429585882368"></a><a id="idm45429585880880"></a><a id="idm45429585879376"></a><a id="idm45429585877872"></a><a id="idm45429585876384"></a><a id="idm45429585874896"></a><a id="idm45429585873408"></a><a id="idm45429585871920"></a><a id="idm45429585870416"></a>
    - These status variables: `Qcache_free_blocks`, `Qcache_free_memory`, `Qcache_hits`, `Qcache_inserts`, `Qcache_lowmem_prunes`, `Qcache_not_cached`, `Qcache_queries_in_cache`, `Qcache_total_blocks`.
        
        <a id="idm45429585862304"></a><a id="idm45429585860816"></a><a id="idm45429585859328"></a><a id="idm45429585857840"></a><a id="idm45429585856352"></a><a id="idm45429585854864"></a><a id="idm45429585853376"></a><a id="idm45429585851888"></a><a id="idm45429585850400"></a><a id="idm45429585848912"></a><a id="idm45429585847424"></a><a id="idm45429585845936"></a><a id="idm45429585844448"></a><a id="idm45429585842960"></a><a id="idm45429585841472"></a><a id="idm45429585839984"></a>
    - These thread states: `checking privileges on cached query`, `checking query cache for query`, `invalidating query cache entries`, `sending cached result to client`, `storing result in query cache`, `Waiting for query cache lock`.
        
        <a id="idm45429585833328"></a><a id="idm45429585831840"></a>
    - The `SQL_CACHE` [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") modifier.
        
        <a id="idm45429585827616"></a><a id="idm45429585826128"></a>
    
    These deprecated query cache items remain deprecated, but have no effect; expect them to be removed in a future MySQL release:
    
    - The `SQL_NO_CACHE` [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html "13.2.10 SELECT Statement") modifier.
        
    - The `ndb_cache_check_time` system variable.
        
    
    The `have_query_cache` system variable remains deprecated, and always has a value of `NO`; expect it to be removed in a future MySQL release.
    
- The data dictionary provides information about database objects, so the server no longer checks directory names in the data directory to find databases. Consequently, the `--ignore-db-dir` option and `ignore_db_dirs` system variables are extraneous and are removed.
    
    <a id="idm45429585814880"></a><a id="idm45429585813392"></a><a id="idm45429585811904"></a><a id="idm45429585810416"></a>
- The DDL log, also known as the metadata log, has been removed. Beginning with MySQL 8.0.3, this functionality is handled by the data dictionary `innodb_ddl_log` table. See [Viewing DDL Logs](https://dev.mysql.com/doc/refman/8.0/en/atomic-ddl.html#atomic-ddl-view-logs "Viewing DDL Logs").
    
- The `tx_isolation` and `tx_read_only` system variables have been removed. Use `transaction_isolation` and `transaction_read_only` instead.
    
    <a id="idm45429585802656"></a><a id="idm45429585801168"></a><a id="idm45429585799680"></a><a id="idm45429585798192"></a>
- The `sync_frm` system variable has been removed because `.frm` files have become obsolete.
    
    <a id="idm45429585794400"></a><a id="idm45429585792912"></a>
- The `secure_auth` system variable and `--secure-auth` client option have been removed. The `MYSQL_SECURE_AUTH` option for the [`mysql_options()`](https://dev.mysql.com/doc/c-api/8.0/en/mysql-options.html) C API function was removed.
    
    <a id="idm45429585787472"></a><a id="idm45429585785984"></a><a id="idm45429585784496"></a><a id="idm45429585783008"></a><a id="idm45429585781520"></a><a id="idm45429585780032"></a>
- The `multi_range_count` system variable is removed.
    
    <a id="idm45429585776992"></a><a id="idm45429585775504"></a>
- The `log_warnings` system variable and `--log-warnings` server option have been removed. Use the [`log_error_verbosity`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_log_error_verbosity) system variable instead.
    
    <a id="idm45429585770464"></a><a id="idm45429585768976"></a><a id="idm45429585767488"></a><a id="idm45429585766000"></a>
- The global scope for the [`sql_log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_sql_log_bin) system variable was removed. `sql_log_bin` has session scope only, and applications that rely on accessing `@@GLOBAL.sql_log_bin` should be adjusted.
    
    <a id="idm45429585760800"></a><a id="idm45429585759312"></a>
- The `metadata_locks_cache_size` and `metadata_locks_hash_instances` system variables are removed.
    
    <a id="idm45429585755520"></a><a id="idm45429585754016"></a><a id="idm45429585752512"></a><a id="idm45429585751008"></a>
- The unused `date_format`, `datetime_format`, `time_format`, and `max_tmp_tables` system variables are removed.
    
    <a id="idm45429585745776"></a><a id="idm45429585744288"></a><a id="idm45429585742800"></a><a id="idm45429585741312"></a><a id="idm45429585739824"></a><a id="idm45429585738336"></a><a id="idm45429585736848"></a><a id="idm45429585735360"></a>
- These deprecated compatibility SQL modes are removed: `DB2`, `MAXDB`, `MSSQL`, `MYSQL323`, `MYSQL40`, `ORACLE`, `POSTGRESQL`, `NO_FIELD_OPTIONS`, `NO_KEY_OPTIONS`, `NO_TABLE_OPTIONS`. They can no longer be assigned to the `sql_mode` system variable or used as permitted values for the [**mysqldump**](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html "4.5.4 mysqldump — A Database Backup Program") [`--compatible`](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html#option_mysqldump_compatible) option.
    
    <a id="idm45429585722656"></a><a id="idm45429585721168"></a><a id="idm45429585719680"></a><a id="idm45429585718224"></a><a id="idm45429585716768"></a><a id="idm45429585715280"></a><a id="idm45429585713792"></a><a id="idm45429585712304"></a><a id="idm45429585710816"></a><a id="idm45429585709328"></a><a id="idm45429585707840"></a><a id="idm45429585706352"></a><a id="idm45429585704864"></a><a id="idm45429585703376"></a><a id="idm45429585701888"></a><a id="idm45429585700400"></a><a id="idm45429585698912"></a><a id="idm45429585697424"></a><a id="idm45429585695936"></a><a id="idm45429585694448"></a><a id="idm45429585692960"></a><a id="idm45429585691472"></a>
    
    Removal of `MAXDB` means that the [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types") data type for [`CREATE TABLE`](https://dev.mysql.com/doc/refman/8.0/en/create-table.html "13.1.20 CREATE TABLE Statement") or [`ALTER TABLE`](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html "13.1.9 ALTER TABLE Statement") is treated as [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types"), and is no longer treated as [`DATETIME`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html "11.2.2 The DATE, DATETIME, and TIMESTAMP Types").
    
- The deprecated `ASC` or `DESC` qualifiers for `GROUP BY` clauses are removed. Queries that previously relied on `GROUP BY` sorting may produce results that differ from previous MySQL versions. To produce a given sort order, provide an `ORDER BY` clause.
    
    <a id="idm45429585678016"></a><a id="idm45429585676528"></a>
- The `EXTENDED` and `PARTITIONS` keywords for the [`EXPLAIN`](https://dev.mysql.com/doc/refman/8.0/en/explain.html "13.8.2 EXPLAIN Statement") statement have been removed. These keywords are unnecessary because their effect is always enabled.
    
    <a id="idm45429585671440"></a><a id="idm45429585669952"></a><a id="idm45429585668464"></a><a id="idm45429585666976"></a>
- These encryption-related items are removed:
    
    - The `ENCODE()` and `DECODE()` functions.
        
        <a id="idm45429585662256"></a><a id="idm45429585660768"></a><a id="idm45429585659280"></a><a id="idm45429585657792"></a>
    - The `ENCRYPT()` function.
        
        <a id="idm45429585654768"></a><a id="idm45429585653280"></a>
    - The `DES_ENCRYPT()`, and `DES_DECRYPT()` functions, the `--des-key-file` option, the `have_crypt` system variable, the `DES_KEY_FILE` option for the [`FLUSH`](https://dev.mysql.com/doc/refman/8.0/en/flush.html "13.7.8.3 FLUSH Statement") statement, and the `HAVE_CRYPT` **CMake** option.
        
        <a id="idm45429585645024"></a><a id="idm45429585643536"></a><a id="idm45429585642048"></a><a id="idm45429585640560"></a><a id="idm45429585639072"></a><a id="idm45429585637584"></a><a id="idm45429585636096"></a><a id="idm45429585634608"></a><a id="idm45429585633120"></a><a id="idm45429585631632"></a><a id="idm45429585630144"></a><a id="idm45429585628656"></a>
    
    In place of the removed encryption functions: For `ENCRYPT()`, consider using [`SHA2()`](https://dev.mysql.com/doc/refman/8.0/en/encryption-functions.html#function_sha2) instead for one-way hashing. For the others, consider using [`AES_ENCRYPT()`](https://dev.mysql.com/doc/refman/8.0/en/encryption-functions.html#function_aes-encrypt) and [`AES_DECRYPT()`](https://dev.mysql.com/doc/refman/8.0/en/encryption-functions.html#function_aes-decrypt) instead.
    
- In MySQL 5.7, several spatial functions available under multiple names were deprecated to move in the direction of making the spatial function namespace more consistent, the goal being that each spatial function name begin with `ST_` if it performs an exact operation, or with `MBR` if it performs an operation based on minimum bounding rectangles. In MySQL 8.0, the deprecated functions are removed to leave only the corresponding `ST_` and `MBR` functions:
    
    - These functions are removed in favor of the `MBR` names: `Contains()`, `Disjoint()`, `Equals()`, `Intersects()`, `Overlaps()`, `Within()`.
        
        <a id="idm45429585611008"></a><a id="idm45429585609520"></a><a id="idm45429585608032"></a><a id="idm45429585606544"></a><a id="idm45429585605056"></a><a id="idm45429585603568"></a><a id="idm45429585602080"></a><a id="idm45429585600592"></a><a id="idm45429585599104"></a><a id="idm45429585597616"></a><a id="idm45429585596128"></a><a id="idm45429585594640"></a>
    - These functions are removed in favor of the `ST_` names: `Area()`, `AsBinary()`, `AsText()`, `AsWKB()`, `AsWKT()`, `Buffer()`, `Centroid()`, `ConvexHull()`, `Crosses()`, `Dimension()`, `Distance()`, `EndPoint()`, `Envelope()`, `ExteriorRing()`, `GeomCollFromText()`, `GeomCollFromWKB()`, `GeomFromText()`, `GeomFromWKB()`, `GeometryCollectionFromText()`, `GeometryCollectionFromWKB()`, `GeometryFromText()`, `GeometryFromWKB()`, `GeometryN()`, `GeometryType()`, `InteriorRingN()`, `IsClosed()`, `IsEmpty()`, `IsSimple()`, `LineFromText()`, `LineFromWKB()`, `LineStringFromText()`, `LineStringFromWKB()`, `MLineFromText()`, `MLineFromWKB()`, `MPointFromText()`, `MPointFromWKB()`, `MPolyFromText()`, `MPolyFromWKB()`, `MultiLineStringFromText()`, `MultiLineStringFromWKB()`, `MultiPointFromText()`, `MultiPointFromWKB()`, `MultiPolygonFromText()`, `MultiPolygonFromWKB()`, `NumGeometries()`, `NumInteriorRings()`, `NumPoints()`, `PointFromText()`, `PointFromWKB()`, `PointN()`, `PolyFromText()`, `PolyFromWKB()`, `PolygonFromText()`, `PolygonFromWKB()`, `SRID()`, `StartPoint()`, `Touches()`, `X()`, `Y()`.
        
        <a id="idm45429585549168"></a><a id="idm45429585547680"></a><a id="idm45429585546192"></a><a id="idm45429585544704"></a><a id="idm45429585543216"></a><a id="idm45429585541728"></a><a id="idm45429585540240"></a><a id="idm45429585538752"></a><a id="idm45429585537264"></a><a id="idm45429585535776"></a><a id="idm45429585534288"></a><a id="idm45429585532800"></a><a id="idm45429585531312"></a><a id="idm45429585529824"></a><a id="idm45429585528336"></a><a id="idm45429585526848"></a><a id="idm45429585525360"></a><a id="idm45429585523872"></a><a id="idm45429585522384"></a><a id="idm45429585520896"></a><a id="idm45429585519408"></a><a id="idm45429585517920"></a><a id="idm45429585516432"></a><a id="idm45429585514944"></a><a id="idm45429585513456"></a><a id="idm45429585511968"></a><a id="idm45429585510480"></a><a id="idm45429585508992"></a><a id="idm45429585507504"></a><a id="idm45429585506016"></a><a id="idm45429585504528"></a><a id="idm45429585503040"></a><a id="idm45429585501552"></a><a id="idm45429585500064"></a><a id="idm45429585498576"></a><a id="idm45429585497088"></a><a id="idm45429585495600"></a><a id="idm45429585494096"></a><a id="idm45429585492592"></a><a id="idm45429585491088"></a><a id="idm45429585489584"></a><a id="idm45429585488096"></a><a id="idm45429585486608"></a><a id="idm45429585485120"></a><a id="idm45429585483632"></a><a id="idm45429585482144"></a><a id="idm45429585480656"></a><a id="idm45429585479168"></a><a id="idm45429585477680"></a><a id="idm45429585476192"></a><a id="idm45429585474704"></a><a id="idm45429585473216"></a><a id="idm45429585471728"></a><a id="idm45429585470240"></a><a id="idm45429585468752"></a><a id="idm45429585467264"></a><a id="idm45429585465776"></a><a id="idm45429585464288"></a><a id="idm45429585462800"></a><a id="idm45429585461312"></a><a id="idm45429585459824"></a><a id="idm45429585458336"></a><a id="idm45429585456848"></a><a id="idm45429585455360"></a><a id="idm45429585453872"></a><a id="idm45429585452384"></a><a id="idm45429585450896"></a><a id="idm45429585449408"></a><a id="idm45429585447920"></a><a id="idm45429585446432"></a><a id="idm45429585444944"></a><a id="idm45429585443456"></a><a id="idm45429585441968"></a><a id="idm45429585440480"></a><a id="idm45429585438992"></a><a id="idm45429585437504"></a><a id="idm45429585436016"></a><a id="idm45429585434512"></a><a id="idm45429585433008"></a><a id="idm45429585431504"></a><a id="idm45429585430000"></a><a id="idm45429585428512"></a><a id="idm45429585427024"></a><a id="idm45429585425536"></a><a id="idm45429585424048"></a><a id="idm45429585422560"></a><a id="idm45429585421072"></a><a id="idm45429585419584"></a><a id="idm45429585418096"></a><a id="idm45429585416608"></a><a id="idm45429585415120"></a><a id="idm45429585413632"></a><a id="idm45429585412144"></a><a id="idm45429585410656"></a><a id="idm45429585409168"></a><a id="idm45429585407680"></a><a id="idm45429585406192"></a><a id="idm45429585404704"></a><a id="idm45429585403216"></a><a id="idm45429585401728"></a><a id="idm45429585400240"></a><a id="idm45429585398752"></a><a id="idm45429585397264"></a><a id="idm45429585395776"></a><a id="idm45429585394288"></a><a id="idm45429585392800"></a><a id="idm45429585391312"></a><a id="idm45429585389824"></a><a id="idm45429585388336"></a><a id="idm45429585386848"></a><a id="idm45429585385360"></a><a id="idm45429585383872"></a><a id="idm45429585382384"></a><a id="idm45429585380896"></a><a id="idm45429585379408"></a><a id="idm45429585377952"></a><a id="idm45429585376496"></a><a id="idm45429585375040"></a>
    - `GLength()` is removed in favor of [`ST_Length()`](https://dev.mysql.com/doc/refman/8.0/en/gis-linestring-property-functions.html#function_st-length).
        
        <a id="idm45429585370656"></a><a id="idm45429585369168"></a>
    
- The functions described in [Section 12.17.4, “Functions That Create Geometry Values from WKB Values”](https://dev.mysql.com/doc/refman/8.0/en/gis-wkb-functions.html "12.17.4 Functions That Create Geometry Values from WKB Values") previously accepted either WKB strings or geometry arguments. Geometry arguments are no longer permitted and produce an error. See that section for guidelines for migrating queries away from using geometry arguments.
    
    <a id="idm45429585365504"></a><a id="idm45429585364048"></a>
- The parser no longer treats `\N` as a synonym for `NULL` in SQL statements. Use `NULL` instead.
    
    <a id="idm45429585359520"></a><a id="idm45429585358032"></a>
    
    This change does not affect text file import or export operations performed with [`LOAD DATA`](https://dev.mysql.com/doc/refman/8.0/en/load-data.html "13.2.7 LOAD DATA Statement") or [`SELECT ... INTO OUTFILE`](https://dev.mysql.com/doc/refman/8.0/en/select-into.html "13.2.10.1 SELECT ... INTO Statement"), for which `NULL` continues to be represented by `\N`. See [Section 13.2.7, “LOAD DATA Statement”](https://dev.mysql.com/doc/refman/8.0/en/load-data.html "13.2.7 LOAD DATA Statement").
    
- `PROCEDURE ANALYSE()` syntax is removed.
    
    <a id="idm45429585349760"></a><a id="idm45429585348272"></a>
- The client-side `--ssl` and `--ssl-verify-server-cert` options have been removed. Use [`--ssl-mode=REQUIRED`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-mode) instead of `--ssl=1` or `--enable-ssl`. Use [`--ssl-mode=DISABLED`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-mode) instead of `--ssl=0`, `--skip-ssl`, or `--disable-ssl`. Use [`--ssl-mode=VERIFY_IDENTITY`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_ssl-mode) instead of `--ssl-verify-server-cert` options. (The server-side [`--ssl`](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_ssl) option is still available, but is deprecated as of MySQL 8.0.26 and subject to removal in a future MySQL version.)
    
    <a id="idm45429585338096"></a><a id="idm45429585336608"></a><a id="idm45429585335120"></a><a id="idm45429585333616"></a>
    
    For the C API, `MYSQL_OPT_SSL_ENFORCE` and `MYSQL_OPT_SSL_VERIFY_SERVER_CERT` options for [`mysql_options()`](https://dev.mysql.com/doc/c-api/8.0/en/mysql-options.html) correspond to the client-side `--ssl` and `--ssl-verify-server-cert` options and are removed. Use `MYSQL_OPT_SSL_MODE` with an option value of `SSL_MODE_REQUIRED` or `SSL_MODE_VERIFY_IDENTITY` instead.
    
    <a id="idm45429585325872"></a><a id="idm45429585324384"></a><a id="idm45429585322896"></a><a id="idm45429585321392"></a>
- The `--temp-pool` server option was removed.
    
    <a id="idm45429585318624"></a><a id="idm45429585317136"></a>
- The `ignore_builtin_innodb` system variable is removed.
    
    <a id="idm45429585314096"></a><a id="idm45429585312608"></a>
- The server no longer performs conversion of pre-MySQL 5.1 database names containing special characters to 5.1 format with the addition of a `#mysql50#` prefix. Because these conversions are no longer performed, the `--fix-db-names` and `--fix-table-names` options for [**mysqlcheck**](https://dev.mysql.com/doc/refman/8.0/en/mysqlcheck.html "4.5.3 mysqlcheck — A Table Maintenance Program"), the `UPGRADE DATA DIRECTORY NAME` clause for the [`ALTER DATABASE`](https://dev.mysql.com/doc/refman/8.0/en/alter-database.html "13.1.2 ALTER DATABASE Statement") statement, and the `Com_alter_db_upgrade` status variable are removed.
    
    <a id="idm45429585304288"></a><a id="idm45429585302784"></a><a id="idm45429585301280"></a><a id="idm45429585299792"></a><a id="idm45429585298304"></a><a id="idm45429585296816"></a><a id="idm45429585295328"></a><a id="idm45429585293840"></a><a id="idm45429585292352"></a><a id="idm45429585290864"></a>
    
    Upgrades are supported only from one major version to another (for example, 5.0 to 5.1, or 5.1 to 5.5), so there should be little remaining need for conversion of older 5.0 database names to current versions of MySQL. As a workaround, upgrade a MySQL 5.0 installation to MySQL 5.1 before upgrading to a more recent release.
    
- The **mysql\_install\_db** program has been removed from MySQL distributions. Data directory initialization should be performed by invoking [**mysqld**](https://dev.mysql.com/doc/refman/8.0/en/mysqld.html "4.3.1 mysqld — The MySQL Server") with the [`--initialize`](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_initialize) or [`--initialize-insecure`](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_initialize-insecure) option instead. In addition, the `--bootstrap` option for [**mysqld**](https://dev.mysql.com/doc/refman/8.0/en/mysqld.html "4.3.1 mysqld — The MySQL Server") that was used by **mysql\_install\_db** was removed, and the `INSTALL_SCRIPTDIR` `CMake` option that controlled the installation location for **mysql\_install\_db** was removed.
    
    <a id="idm45429585278944"></a><a id="idm45429585277456"></a><a id="idm45429585275968"></a><a id="idm45429585274480"></a><a id="idm45429585272992"></a><a id="idm45429585271504"></a>
- The generic partitioning handler was removed from the MySQL server. In order to support partitioning of a given table, the storage engine used for the table must now provide its own (“native”) partitioning handler. The `--partition` and `--skip-partition` options are removed from the MySQL Server, and partitioning-related entries are no longer shown in the output of [`SHOW PLUGINS`](https://dev.mysql.com/doc/refman/8.0/en/show-plugins.html "13.7.7.25 SHOW PLUGINS Statement") or in the [`INFORMATION_SCHEMA.PLUGINS`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-plugins-table.html "26.3.22 The INFORMATION_SCHEMA PLUGINS Table") table.
    
    <a id="idm45429585264752"></a><a id="idm45429585263264"></a><a id="idm45429585261776"></a><a id="idm45429585260288"></a>
    
    Two MySQL storage engines currently provide native partitioning support: [`InnoDB`](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html "Chapter 15 The InnoDB Storage Engine") and [`NDB`](https://dev.mysql.com/doc/refman/8.0/en/mysql-cluster.html "Chapter 23 MySQL NDB Cluster 8.0"). Of these, only `InnoDB` is supported in MySQL 8.0. Any attempt to create partitioned tables in MySQL 8.0 using any other storage engine fails.
    
    **Ramifications for upgrades. ** The direct upgrade of a partitioned table using a storage engine other than `InnoDB` (such as [`MyISAM`](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html "16.2 The MyISAM Storage Engine")) from MySQL 5.7 (or earlier) to MySQL 8.0 is not supported. There are two options for handling such a table:
    
    - Remove the table's partitioning, using [`ALTER TABLE ... REMOVE PARTITIONING`](https://dev.mysql.com/doc/refman/8.0/en/alter-table-partition-operations.html "13.1.9.1 ALTER TABLE Partition Operations").
        
    - Change the storage engine used for the table to `InnoDB`, with [`ALTER TABLE ... ENGINE=INNODB`](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html "13.1.9 ALTER TABLE Statement").
        
    
    At least one of the two operations just listed must be performed for each partitioned non-`InnoDB` table prior to upgrading the server to MySQL 8.0. Otherwise, such a table cannot be used following the upgrade.
    
    Due to the fact that table creation statements that would result in a partitioned table using a storage engine without partitioning support now fail with an error (ER\_CHECK\_NOT_IMPLEMENTED), you must make sure that any statements in a dump file (such as that written by [**mysqldump**](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html "4.5.4 mysqldump — A Database Backup Program")) from an older version of MySQL that you wish to import into a MySQL 8.0 server that create partitioned tables do not also specify a storage engine such as `MyISAM` that has no native partitioning handler. You can do this by performing either of the following:
    
    - Remove any references to partitioning from `CREATE TABLE` statements that use a value for the `STORAGE ENGINE` option other than `InnoDB`.
        
    - Specifying the storage engine as `InnoDB`, or allow `InnoDB` to be used as the table's storage engine by default.
        
    
    For more information, see [Section 24.6.2, “Partitioning Limitations Relating to Storage Engines”](https://dev.mysql.com/doc/refman/8.0/en/partitioning-limitations-storage-engines.html "24.6.2 Partitioning Limitations Relating to Storage Engines").
    
- System and status variable information is no longer maintained in the `INFORMATION_SCHEMA`. These tables are removed: `GLOBAL_VARIABLES`, `SESSION_VARIABLES`, `GLOBAL_STATUS`, `SESSION_STATUS`. Use the corresponding Performance Schema tables instead. See [Section 27.12.14, “Performance Schema System Variable Tables”](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-system-variable-tables.html "27.12.14 Performance Schema System Variable Tables"), and [Section 27.12.15, “Performance Schema Status Variable Tables”](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-status-variable-tables.html "27.12.15 Performance Schema Status Variable Tables"). In addition, the `show_compatibility_56` system variable was removed. It was used in the transition period during which system and status variable information in `INFORMATION_SCHEMA` tables was moved to Performance Schema tables, and is no longer needed. These status variables are removed: `Slave_heartbeat_period`, `Slave_last_heartbeat`, `Slave_received_heartbeats`, `Slave_retried_transactions`, `Slave_running`. The information they provided is available in Performance Schema tables; see [Migrating to Performance Schema System and Status Variable Tables](https://dev.mysql.com/doc/refman/5.7/en/performance-schema-variable-table-migration.html).
    
    <a id="idm45429585221088"></a><a id="idm45429585219600"></a><a id="idm45429585218112"></a><a id="idm45429585216624"></a><a id="idm45429585215136"></a><a id="idm45429585213648"></a><a id="idm45429585212160"></a><a id="idm45429585210672"></a><a id="idm45429585209184"></a><a id="idm45429585207696"></a><a id="idm45429585206208"></a><a id="idm45429585204720"></a><a id="idm45429585203232"></a><a id="idm45429585201744"></a><a id="idm45429585200256"></a><a id="idm45429585198752"></a><a id="idm45429585197248"></a><a id="idm45429585195744"></a><a id="idm45429585194240"></a><a id="idm45429585192752"></a>
- The Performance Schema `setup_timers` table was removed, as was the `TICK` row in the [`performance_timers`](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-performance-timers-table.html "27.12.21.4 The performance_timers Table") table.
    
    <a id="idm45429585187536"></a><a id="idm45429585186048"></a><a id="idm45429585184560"></a><a id="idm45429585183072"></a>
- The `libmysqld` embedded server library is removed, along with:
    
    <a id="idm45429585180016"></a><a id="idm45429585178528"></a><a id="idm45429585177040"></a><a id="idm45429585175552"></a>
    
    - The [`mysql_options()`](https://dev.mysql.com/doc/c-api/8.0/en/mysql-options.html) `MYSQL_OPT_GUESS_CONNECTION`, `MYSQL_OPT_USE_EMBEDDED_CONNECTION`, `MYSQL_OPT_USE_REMOTE_CONNECTION`, and `MYSQL_SET_CLIENT_IP` options
        
    - The [**mysql_config**](https://dev.mysql.com/doc/refman/8.0/en/mysql-config.html "4.7.1 mysql_config — Display Options for Compiling Clients") `--libmysqld-libs`, `--embedded-libs`, and `--embedded` options
        
    - The **CMake** `WITH_EMBEDDED_SERVER`, `WITH_EMBEDDED_SHARED_LIBRARY`, and `INSTALL_SECURE_FILE_PRIV_EMBEDDEDDIR` options
        
    - The (undocumented) [**mysql**](https://dev.mysql.com/doc/refman/8.0/en/mysql.html "4.5.1 mysql — The MySQL Command-Line Client") `--server-arg` option
        
    - The **mysqltest** `--embedded-server`, `--server-arg`, and `--server-file` options
        
    - The **mysqltest_embedded** and **mysql\_client\_test_embedded** test programs
        
    
- The **mysql_plugin** utility was removed. Alternatives include loading plugins at server startup using the [`--plugin-load`](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_plugin-load) or [`--plugin-load-add`](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_plugin-load-add) option, or at runtime using the [`INSTALL PLUGIN`](https://dev.mysql.com/doc/refman/8.0/en/install-plugin.html "13.7.4.4 INSTALL PLUGIN Statement") statement.
    
    <a id="idm45429585149664"></a><a id="idm45429585148176"></a>
- The **resolveip** utility is removed. **nslookup**, **host**, or **dig** can be used instead.
    
    <a id="idm45429585142992"></a><a id="idm45429585141504"></a>
- The **resolve\_stack\_dump** utility is removed. Stack traces from official MySQL builds are always symbolized, so there is no need to use **resolve\_stack\_dump**.
    
    <a id="idm45429585137648"></a><a id="idm45429585136160"></a>
- The following server error codes are not used and have been removed. Applications that test specifically for any of these errors should be updated.
    
    <a id="idm45429585133696"></a><a id="idm45429585132208"></a>
    
    ```none
    ER_BINLOG_READ_EVENT_CHECKSUM_FAILURE
    ER_BINLOG_ROW_RBR_TO_SBR
    ER_BINLOG_ROW_WRONG_TABLE_DEF
    ER_CANT_ACTIVATE_LOG
    ER_CANT_CHANGE_GTID_NEXT_IN_TRANSACTION
    ER_CANT_CREATE_FEDERATED_TABLE
    ER_CANT_CREATE_SROUTINE
    ER_CANT_DELETE_FILE
    ER_CANT_GET_WD
    ER_CANT_SET_GTID_PURGED_WHEN_GTID_MODE_IS_OFF
    ER_CANT_SET_WD
    ER_CANT_WRITE_LOCK_LOG_TABLE
    ER_CREATE_DB_WITH_READ_LOCK
    ER_CYCLIC_REFERENCE
    ER_DB_DROP_DELETE
    ER_DELAYED_NOT_SUPPORTED
    ER_DIFF_GROUPS_PROC
    ER_DISK_FULL
    ER_DROP_DB_WITH_READ_LOCK
    ER_DROP_USER
    ER_DUMP_NOT_IMPLEMENTED
    ER_ERROR_DURING_CHECKPOINT
    ER_ERROR_ON_CLOSE
    ER_EVENTS_DB_ERROR
    ER_EVENT_CANNOT_DELETE
    ER_EVENT_CANT_ALTER
    ER_EVENT_COMPILE_ERROR
    ER_EVENT_DATA_TOO_LONG
    ER_EVENT_DROP_FAILED
    ER_EVENT_MODIFY_QUEUE_ERROR
    ER_EVENT_NEITHER_M_EXPR_NOR_M_AT
    ER_EVENT_OPEN_TABLE_FAILED
    ER_EVENT_STORE_FAILED
    ER_EXEC_STMT_WITH_OPEN_CURSOR
    ER_FAILED_ROUTINE_BREAK_BINLOG
    ER_FLUSH_MASTER_BINLOG_CLOSED
    ER_FORM_NOT_FOUND
    ER_FOUND_GTID_EVENT_WHEN_GTID_MODE_IS_OFF__UNUSED
    ER_FRM_UNKNOWN_TYPE
    ER_GOT_SIGNAL
    ER_GRANT_PLUGIN_USER_EXISTS
    ER_GTID_MODE_REQUIRES_BINLOG
    ER_GTID_NEXT_IS_NOT_IN_GTID_NEXT_LIST
    ER_HASHCHK
    ER_INDEX_REBUILD
    ER_INNODB_NO_FT_USES_PARSER
    ER_LIST_OF_FIELDS_ONLY_IN_HASH_ERROR
    ER_LOAD_DATA_INVALID_COLUMN_UNUSED
    ER_LOGGING_PROHIBIT_CHANGING_OF
    ER_MALFORMED_DEFINER
    ER_MASTER_KEY_ROTATION_ERROR_BY_SE
    ER_NDB_CANT_SWITCH_BINLOG_FORMAT
    ER_NEVER_USED
    ER_NISAMCHK
    ER_NO_CONST_EXPR_IN_RANGE_OR_LIST_ERROR
    ER_NO_FILE_MAPPING
    ER_NO_GROUP_FOR_PROC
    ER_NO_RAID_COMPILED
    ER_NO_SUCH_KEY_VALUE
    ER_NO_SUCH_PARTITION__UNUSED
    ER_OBSOLETE_CANNOT_LOAD_FROM_TABLE
    ER_OBSOLETE_COL_COUNT_DOESNT_MATCH_CORRUPTED
    ER_ORDER_WITH_PROC
    ER_PARTITION_SUBPARTITION_ERROR
    ER_PARTITION_SUBPART_MIX_ERROR
    ER_PART_STATE_ERROR
    ER_PASSWD_LENGTH
    ER_QUERY_ON_MASTER
    ER_RBR_NOT_AVAILABLE
    ER_SKIPPING_LOGGED_TRANSACTION
    ER_SLAVE_CHANNEL_DELETE
    ER_SLAVE_MULTIPLE_CHANNELS_HOST_PORT
    ER_SLAVE_MUST_STOP
    ER_SLAVE_WAS_NOT_RUNNING
    ER_SLAVE_WAS_RUNNING
    ER_SP_GOTO_IN_HNDLR
    ER_SP_PROC_TABLE_CORRUPT
    ER_SQL_MODE_NO_EFFECT
    ER_SR_INVALID_CREATION_CTX
    ER_TABLE_NEEDS_UPG_PART
    ER_TOO_MUCH_AUTO_TIMESTAMP_COLS
    ER_UNEXPECTED_EOF
    ER_UNION_TABLES_IN_DIFFERENT_DIR
    ER_UNSUPPORTED_BY_REPLICATION_THREAD
    ER_UNUSED1
    ER_UNUSED2
    ER_UNUSED3
    ER_UNUSED4
    ER_UNUSED5
    ER_UNUSED6
    ER_VIEW_SELECT_DERIVED_UNUSED
    ER_WRONG_MAGIC
    ER_WSAS_FAILED
    ```
    
- The deprecated `INFORMATION_SCHEMA` `INNODB_LOCKS` and `INNODB_LOCK_WAITS` tables are removed. Use the Performance Schema [`data_locks`](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-data-locks-table.html "27.12.13.1 The data_locks Table") and [`data_lock_waits`](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-data-lock-waits-table.html "27.12.13.2 The data_lock_waits Table") tables instead.
    
    <a id="idm45429585119264"></a><a id="idm45429585117776"></a><a id="idm45429585116288"></a><a id="idm45429585114800"></a>
    
    Note
    
    In MySQL 5.7, the `LOCK_TABLE` column in the `INNODB_LOCKS` table and the `locked_table` column in the `sys` schema [`innodb_lock_waits`](https://dev.mysql.com/doc/refman/8.0/en/sys-innodb-lock-waits.html "28.4.3.9 The innodb_lock_waits and x$innodb_lock_waits Views") and [`x$innodb_lock_waits`](https://dev.mysql.com/doc/refman/8.0/en/sys-innodb-lock-waits.html "28.4.3.9 The innodb_lock_waits and x$innodb_lock_waits Views") views contain combined schema/table name values. In MySQL 8.0, the [`data_locks`](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-data-locks-table.html "27.12.13.1 The data_locks Table") table and the `sys` schema views contain separate schema name and table name columns. See [Section 28.4.3.9, “The innodb\_lock\_waits and x$innodb\_lock\_waits Views”](https://dev.mysql.com/doc/refman/8.0/en/sys-innodb-lock-waits.html "28.4.3.9 The innodb_lock_waits and x$innodb_lock_waits Views").
    
- `InnoDB` no longer supports compressed temporary tables. When [`innodb_strict_mode`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_strict_mode) is enabled (the default), [`CREATE TEMPORARY TABLE`](https://dev.mysql.com/doc/refman/8.0/en/create-table.html "13.1.20 CREATE TABLE Statement") returns an error if `ROW_FORMAT=COMPRESSED` or `KEY_BLOCK_SIZE` is specified. If [`innodb_strict_mode`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_strict_mode) is disabled, warnings are issued and the temporary table is created using a non-compressed row format.
    
    <a id="idm45429585097040"></a><a id="idm45429585095584"></a>
- `InnoDB` no longer creates `.isl` files (`InnoDB` Symbolic Link files) when creating tablespace data files outside of the MySQL data directory. The [`innodb_directories`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_directories) option now supports locating tablespace files created outside of the data directory.
    
    With this change, moving a remote tablespace while the server is offline by manually modifying an `.isl` file is no longer supported. Moving remote tablespace files is now supported by the [`innodb_directories`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_directories) option. See [Section 15.6.3.6, “Moving Tablespace Files While the Server is Offline”](https://dev.mysql.com/doc/refman/8.0/en/innodb-moving-data-files-offline.html "15.6.3.6 Moving Tablespace Files While the Server is Offline").
    
    <a id="idm45429585086272"></a><a id="idm45429585084816"></a>
- The following `InnoDB` file format variables were removed:
    
    - `innodb_file_format`
        
        <a id="idm45429585080144"></a><a id="idm45429585078656"></a>
    - `innodb_file_format_check`
        
        <a id="idm45429585075696"></a><a id="idm45429585074192"></a>
    - `innodb_file_format_max`
        
        <a id="idm45429585071232"></a><a id="idm45429585069744"></a>
    - `innodb_large_prefix`
        
        <a id="idm45429585066800"></a><a id="idm45429585065312"></a>
    
    File format variables were necessary for creating tables compatible with earlier versions of `InnoDB` in MySQL 5.1. Now that MySQL 5.1 has reached the end of its product lifecycle, these options are no longer required.
    
    The `FILE_FORMAT` column was removed from the [`INNODB_TABLES`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-tables-table.html "26.4.23 The INFORMATION_SCHEMA INNODB_TABLES Table") and [`INNODB_TABLESPACES`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-innodb-tablespaces-table.html "26.4.24 The INFORMATION_SCHEMA INNODB_TABLESPACES Table") Information Schema tables.
    
    <a id="idm45429585058288"></a><a id="idm45429585056800"></a>
- The `innodb_support_xa` system variable, which enables support for two-phase commit in XA transactions, was removed. `InnoDB` support for two-phase commit in XA transactions is always enabled.
    
    <a id="idm45429585052896"></a><a id="idm45429585051408"></a>
- Support for DTrace was removed.
    
    <a id="idm45429585049088"></a><a id="idm45429585047600"></a>
- The `JSON_APPEND()` function was removed. Use [`JSON_ARRAY_APPEND()`](https://dev.mysql.com/doc/refman/8.0/en/json-modification-functions.html#function_json-array-append) instead.
    
    <a id="idm45429585043168"></a><a id="idm45429585041680"></a>
- Support for placing table partitions in shared `InnoDB` tablespaces was removed in MySQL 8.0.13. Shared tablespaces include the `InnoDB` system tablespace and general tablespaces. For information about identifying partitions in shared tablespaces and moving them to file-per-table tablespaces, see [Section 2.11.5, “Preparing Your Installation for Upgrade”](https://dev.mysql.com/doc/refman/8.0/en/upgrade-prerequisites.html "2.11.5 Preparing Your Installation for Upgrade").
    
    <a id="idm45429585036896"></a><a id="idm45429585035392"></a>
- Support for setting user variables in statements other than [`SET`](https://dev.mysql.com/doc/refman/8.0/en/set-variable.html "13.7.6.1 SET Syntax for Variable Assignment") was deprecated in MySQL 8.0.13. This functionality is subject to removal in MySQL 9.0.
    
    <a id="idm45429585031536"></a><a id="idm45429585030048"></a>
- The `--ndb` [**perror**](https://dev.mysql.com/doc/refman/8.0/en/perror.html "4.8.2 perror — Display MySQL Error Message Information") option was removed. Use the [**ndb_perror**](https://dev.mysql.com/doc/refman/8.0/en/mysql-cluster-programs-ndb-perror.html "23.5.16 ndb_perror — Obtain NDB Error Message Information") utility instead.
    
    <a id="idm45429585024384"></a><a id="idm45429585022896"></a>
- The `innodb_undo_logs` variable was removed. The [`innodb_rollback_segments`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_rollback_segments) variables performs the same function and should be used instead.
    
    <a id="idm45429585018544"></a><a id="idm45429585017056"></a>
- The `Innodb_available_undo_logs` status variable was removed. The number of available rollback segments per tablespace may be retrieved using `SHOW VARIABLES LIKE 'innodb_rollback_segments';`
    
    <a id="idm45429585013184"></a><a id="idm45429585011680"></a>
- As of MySQL 8.0.14, the previously deprecated [`innodb_undo_tablespaces`](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_undo_tablespaces) variable is no longer configurable. For more information, see [Section 15.6.3.4, “Undo Tablespaces”](https://dev.mysql.com/doc/refman/8.0/en/innodb-undo-tablespaces.html "15.6.3.4 Undo Tablespaces").
    
    <a id="idm45429585007152"></a><a id="idm45429585005664"></a>
- Support for the `ALTER TABLE ... UPGRADE PARTITIONING` statement has been removed.
    
    <a id="idm45429585002592"></a><a id="idm45429585001088"></a>
- As of MySQL 8.0.16, support for the [`internal_tmp_disk_storage_engine`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_internal_tmp_disk_storage_engine) system variable has been removed; internal temporary tables on disk now always use the [`InnoDB`](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html "Chapter 15 The InnoDB Storage Engine") storage engine. See [Storage Engine for On-Disk Internal Temporary Tables](https://dev.mysql.com/doc/refman/8.0/en/internal-temporary-tables.html#internal-temporary-tables-engines-disk "Storage Engine for On-Disk Internal Temporary Tables"),for more information.
    
    <a id="idm45429584995200"></a><a id="idm45429584993744"></a>
- The [`DISABLE_SHARED`](https://dev.mysql.com/doc/refman/8.0/en/source-configuration-options.html#option_cmake_disable_shared) **CMake** option was unused and has been removed.