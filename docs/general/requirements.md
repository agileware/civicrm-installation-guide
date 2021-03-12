# Installation Requirements

Ensure that your system meets the following requirements before installing or upgrading CiviCRM.

If you are curious what technologies other organizations are using to run CiviCRM, you can look at the [CiviCRM Stats](https://stats.civicrm.org/?tab=technology).

## Summary {:#summary}

A recommended server environment should typically meet these guidelines:

* __Operating system__: Linux
* __CMS__: Backdrop, Drupal 7, Drupal 8/9, Joomla, or WordPress
* __PHP__: 7.2 or 7.3 -- with configuration and extension requirements
* __MySQL__: MySQL 5.7+ or MariaDB 10.0+ -- with configuration requirements

Other environments may also meet the system requirements. The following sections present requirements more precisely.

## Server Environment {:#server}

### Operating System {:#os}

If your server meets all of the requirements described on this page, CiviCRM *should* function correctly. There are no explicit operating system requirements. However, it's worth noting that CiviCRM is far more widely deployed and tested on UNIX-based operating systems, in particular Linux (e.g. Ubuntu, Debian, etc.), than with Microsoft Windows. You can still use CiviCRM fine from Windows desktops when the hosting environment runs Linux.

### Hosting

In general, CiviCRM is a demanding web application which requires substantial server resources. It may not perform well on all hosting platforms. Learn more about [choosing your hosting platform](../planning/hosting.md).

## CMS {:#cms}

A CMS, or Content Management System, is a type of application which controls and manages the content of a website. CiviCRM must be installed within one of these compatible CMS platforms.

See the page on [choosing a CMS](../planning/cms.md) for more information about the advantages and disadvantages of each compatible CMS platform.

### Backdrop

* [Backdrop](../backdrop/index.md) 1.0 or newer is required.

### Drupal

* [Drupal 8/9](../drupal8/index.md)

* [Drupal 7](../drupal7/index.md)

### Joomla

* [Joomla](../joomla/index.md) 3.x.x is required.

### WordPress

* [WordPress](../wordpress/index.md) 4.9 or newer is required.

## PHP {:#php}

### PHP Version on the Command Line

The PHP version used on the command line is important and should match the version used by the web server. Using PHP 7.1 (or lower) on the command line and using PHP 7.2 (or higher) on the web server can cause issues.

It is also important to ensure that the same PHP extensions/modules are loaded on the command line and the web server.

### PHP Version

| | CiviCRM 5.27 ESR | CiviCRM 5.x.x stable |
| ---- | ---- | ---- |
| PHP 7.4 | **incompatible** | Testing has shown that as of CiviCRM 5.28 can be run but issues are still being worked on in this [lab issue](https://lab.civicrm.org/dev/core/-/issues/1496) |
| PHP 7.3 | compatible and **recommended** | compatible and **recommended** |
| PHP 7.2 | compatible and **recommended** - but see note below about resaving the SMTP password | compatible and **recommended** but see note below about resaving the SMTP password |
| PHP 7.1 | compatible but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in Dec 2019 | **incompatible** as of 5.35.0 |
| PHP 7.0 | **incompatible** | **incompatible** as of 5.25.0 |
| PHP 5.6 | **incompatible** | **incompatible** |

??? info "Historical PHP Versions"
    The table below gives information on PHP minimum and recommended version compatibility for old versions of CiviCRM:

    | CiviCRM Version          | PHP Minimum Version | PHP Recommended Version |
    | ------------------------ | ------------------- | ----------------------- |
    | 5.1 - 5.5                | 5.5                 | 7.0                     |
    | 5.6 - 5.9                | 5.5                 | 7.1                     |
    | 5.10 - 5.13              | 5.6                 | 7.2                     |
    | 5.14 - 5.23              | 7.0                 | 7.2                     |
    | 5.24                     | 7.0                 | 7.3                     |
    | 5.25 - 5.34              | 7.1                 | 7.3                     |
    | 5.35 - Current           | 7.2                 | 7.3                     |

### PHP Extensions

To install these extensions, you will typically install a separate package within your server's package manager (e.g. `apt-get` on Ubuntu).

#### Required for CiviCRM Core

* [PHP BCMath](https://www.php.net/bcmath) - required for calculating financial values in CiviCRM Core.
* [PHP Curl](https://www.php.net/curl) - required for many payment processors, the extension manager, and the CiviCRM News dashlet.
* [PHP DOM XML](https://www.php.net/manual/en/dom.setup.php) - required by CiviCase.
* [PHP Multibyte](https://php.net/manual/en/ref.mbstring.php) - required for internationalisation and proper encoding of fields.
* [PHP Zip](https://php.net/manual/en/book.zip.php) - required for unzipping auto-downloaded extensions so they can be installed from the browser.
* [PHP INTL](https://www.php.net/intl) - required for outputting localized formatted number strings from CiviCRM 5.28 onwards

#### Required for Third-Party Functionality or CiviCRM Extensions

* [PHP SOAP](https://www.php.net/soap) - required to use the SOAP processor (required for the CiviSMTP service)

#### PHP 7.1 and the MCrypt library

* [PHP MCrypt](https://php.net/manual/en/intro.mcrypt.php) - the MCrypt extension is no longer recommended for new installations.

    !!! warning "PHP 7.2 Compatibility"
        7.2 upgrade warning - 7.2 does not support MCrypt and if MCrypt is not installed the SMTP password (if entered) will need to be re-saved once you update your PHP version to 7.2.
    !!! warning "PHP 7.1 cannot access SMTP credentials"
        CiviCRM will incorrectly attempt to decrypt the SMTP password using the MCrypt library when executed using PHP 7.1. If PHP 7.2 or higher was used to save the SMTP password. This can occur if the PHP version on the command line does not match the web server version.

### PHP Configuration

* Set `memory_limit` between 256 and 512 megabytes
* Don't enable the deprecated `open_basedir` or `safemode` PHP directives. Otherwise you will have an error when automatically installing most of the extensions.
* Don't use MAMP XCache - Several people have reported "white screen of death" trying to run CiviCRM with MAMP's XCache enabled (check MAMP > Preferences > PHP > Cache).

## MySQL

CiviCRM requires MySQL (or compatible) database software.

[MariaDB](https://mariadb.org/) and [Percona](https://www.percona.com/software/mysql-database/percona-server) are forks of the MySQL project and can be used as drop-in replacements for MySQL.

Other database servers (such as PostgreSQL) are not compatible with CiviCRM.

### MySQL Version

Your MySQL version should be **5.7.5 or greater** or MariaDB **10.0.2 or greater**.  As of version 5.28 the minimum install version for CiviCRM is 5.6 users on versions before that are advised to upgrade their MySQL instance to a recommended version. CiviCRM may not run correctly on MySQL 5.6 as these versions don't support some of the functionality CiviCRM uses to ensure that database actions don't compete for the same resources.

??? info "Historical MySQL Versions"
    The table below gives information on MySQL minimum and recommended version compatibility for old versions of CiviCRM:

    | CiviCRM Version          | MySQL Minimum Version | MySQL Recommended Version | MariaDB Minimum Version | MariaDB Recommended Version |
    | ------------------------ | --------------------- | ------------------------- | ----------------------- | ------------------- |
    | 5.26 - 5.27              | 5.5                   | 5.7                       |                         |      |
    | 5.28 - 5.33              | 5.6.5                 | 5.7                       |                         | 10.4 |
    | 5.34 - Current           | 5.7                   | 5.7                       | 10.0.2                  | 10.4 |

#### MySQL 8

As of version 5.24 CiviCRM has been shown to be able to run on MySQL 8 through the execution of our test matrix. Not all of the Content Management Systems support MySQL 8, CiviCRM MySQL 8 support is being tracked in this [open issue for MySQL 8 support](https://lab.civicrm.org/dev/core/issues/392). It is also worth knowing that both [Backdrop](https://forum.backdropcms.org/forum/installing-backdrop-1126-mysql-8-sqlmode-cant-be-set-value-noautocreateuser) and [Drupal 7](https://www.drupal.org/project/drupal/issues/2978575) have open issues with regards to MySQL 8 support. [Drupal 8](https://www.drupal.org/docs/8/system-requirements/database-server) [supports MySQL 8 as of version 8.6](https://www.drupal.org/project/drupal/issues/2966523), Current versions of WordPress and [Joomla](https://docs.joomla.org/Joomla_and_MySQL_8) appear to be compatible with MySQL 8

### MySQL Connection {:#mysql-connection}

By default, new installations of CiviCRM will copy the MySQL connection details from the CMS, creating a shared database.  It is also possible to
install CiviCRM on a separate database. As a rule of thumb:

* *A shared database* works well for small deployments (eg a few thousand records and a single administrator or developer).
* *Separate databases* work well for large deployments (eg a million records and multiple administrators/developers).

If you are unsure which style fits better, consider some trade-offs:

* _Initial setup_: The shared database can be setup automatically. To use a separate database, you must create the second one.
* _"Views" integration_: On Drupal 7 / Backdrop, the "Views" integration can query MySQL data from both
  CMS and Civi. This works easily with a shared database, but it requires effort with separate databases.
* _Backups_: If you have a small data-set and a good backup mechanism, the shared database is easier to backup --
  it's only one job.
* _Large datasets_: If the CMS dataset and Civi dataset grow large (eg millions of records), then separating the
  databases will allow better resource-management.
* _Development/staging process_: If you have different people working on the system code or system configuration,
  then they may need to work with different subsets of the data. (Ex: A theme developer may need access to
  the CMS data but not the Civi data. For an application developer, that could be reversed.) With separate
  databases, it may be easier to negotiate the workflow.

If your choose to use separate databases, then you need connection details for the second database. Connections are represented in URL format. For example:

```
mysql://dbuser:dbpassword@localhost:3306/dbname
```

The example URL captures several pieces of information:

| Setting | Example Value |
| ------- | ----- |
| Database User Name | `dbuser` |
| Database User Password | `dbpassword` |
| Database Server | `localhost` |
| Database Port | `3306` |
| Database Name | `dbname` |

Additional URL parameters may be added to support [MySQL with TLS](mysql_tls.md).

### MySQL Configuration

* Support for the `innodb` storage engine is required.
* The `thread_stack` configuration variable should be set to 192k or higher.
* Trigger support is required.
* The `ONLY_FULL_GROUP_BY` mode should be turned off on production sites - although this is mostly precautionary now.
    * In MySQL 5.7+ the SQL mode `ONLY_FULL_GROUP_BY` is enabled by default which causes some errors due to some of CiviCRM's SQL queries that were written with the assumption that this mode would be disabled.
    * Administrators are advised to turn off this SQL mode by removing the string `ONLY_FULL_GROUP_BY` from the `sql_mode` variable. The following SQL query will do the trick.

        ```sql
        SET GLOBAL sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
        ```

    * See also:
        * A popular [Stack Exchange question](https://stackoverflow.com/a/36033983/895563) discussing this issue
        * [MySQL documentation on `sql_mode`](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sql-mode-setting)
* If you plan to have multiple languages used in your CiviCRM installation it is strongly recommended that you ensure that `locale` is set to a UTF8 locale and also ensure that you set utf8 as the standard encoding for MySQL. To alter locale you can configure as per [Ubuntu instructions](https://www.thomas-krenn.com/en/wiki/Configure_Locales_in_Ubuntu), [Debian](https://wiki.debian.org/Locale), [CentOS](https://www.rosehosting.com/blog/how-to-set-up-system-locale-on-centos-7/). To set the default encoding for MySQL you should follow the steps provided in this [Stack Overflow post](https://stackoverflow.com/questions/3513773/change-mysql-default-character-set-to-utf-8-in-my-cnf)
* In order to support a future data migration from `utf8` to the `utf8mb4` character set, it is recommended, though not yet required, to add the following configuration directives on MySQL 5.5 or 5.6 (these are the default values on MySQL 5.7):

    ```ini
    [mysqld]
    innodb_large_prefix=true
    innodb_file_format=barracuda
    innodb_file_per_table=true
    ```

* In MySQL 8 the default authentication plugin changes from `mysql_native_password` to `caching_sha2_password`. This may cause issues with your PHP layer. Fortunately you can revert this change by specifying your chosen authentication plugin in your MySQL configuration:
  
  ```ini
   [mysqld]
   default_authentication_plugin=mysql_native_password
  ```

  Also in MySQL 8 the following variables, used fairly extensively in CiviCRM installs, have been removed `innodb_large_prefix` and `innodb_file_format`. In MySQL 8 binary logging is also turned on by default which many users may want to turn off, this can be achieved by adding to your MySQL configuration file:

  ```ini
   [mysqld]
   skip-log-bin
  ```

#### MySQL Time

CiviCRM performs various operations based on dates and times – for example, deactivating expired records or triggering scheduled reminders. To perform these operations correctly, the dates and times reported by PHP and MySQL should match. If the dates or times are mismatched, then one or more of the following may be the problem:

* PHP and MySQL may be running on different servers, and the clocks may be out-of-sync. Configuring automatic clock synchronization is the best solution.
* PHP, MySQL, and/or the operating system may be configured to use different default timezones. Verify the configuration of each.
* The content management system (Drupal, Joomla, or WordPress) or one of its plugins may manipulate the timezone settings without informing CiviCRM's. You may wish to post to [Stack Exchange](https://civicrm.stackexchange.com/) or [Mattermost](https://chat.civicrm.org) about your problem. Please include any available details about the timezone settings in the operating system, PHP, MySQL, and the CMS; if you have any special CMS plugins or configuration options which may affect timezones, please report them.

#### MySQL/PHP Time Mismatch Warning - WordPress

If you get a MySQL/PHP mismatch warning, under `Settings > General` set the timezone to a City, not UTC offset.  For example, use `London` not `UTC+0`.

### MySQL Permissions

The permissions you'll need to assign to the MySQL user that CiviCRM uses will **depend on your version of MySQL**. The following example assumes you have a database called `civicrm` and a MySQL user called `civicrm_user`.

```sql
GRANT
  SELECT,
  INSERT,
  UPDATE,
  DELETE,
  CREATE,
  DROP,
  INDEX,
  ALTER,
  CREATE TEMPORARY TABLES,
  LOCK TABLES,
  TRIGGER,
  CREATE ROUTINE,
  ALTER ROUTINE,
  REFERENCES,
  CREATE VIEW,
  SHOW VIEW
ON civicrm.*
TO 'civicrm_user'@'localhost'
IDENTIFIED BY 'realpasswordhere';
```

#### Binary Logging

If you want to enable binary logging you will need to choose one of the following. Either:

* Add the following line to your `/etc/my.cnf` file.

    ```ini
    log_bin_trust_function_creators = 1
    ```

    (See the [MySQL manual reference](http://dev.mysql.com/doc/refman/5.1/en/server-system-variables.html#sysvar_log_bin_trust_function_creators) for more information.)

* Or give the `SUPER` permission (even if your MySQL version is greater than 5.1.6).

    ```sql
    GRANT SUPER ON *.* TO 'civicrm_user'@'localhost';
    ```

    (More information on triggers is available in the MySQL documentation about [Binary Logging of Stored Programs](http://dev.mysql.com/doc/refman/5.1/en/stored-programs-logging.html).)
