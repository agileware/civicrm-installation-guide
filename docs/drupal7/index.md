## Scope of this guide and alternative installation methods

This guide covers standard installation of CiviCRM for production use. For installing a development environment, refer to the [section on Buildkit in the Developer Documentation](https://docs.civicrm.org/dev/en/latest/tools/buildkit/).

## Before installing

1. Ensure that your system meets the [requirements](../general/requirements.md).
1. Install Drupal 7 by referring to the [Drupal 7 Installation Guide](https://www.drupal.org/docs/7/install) if needed.

## Determine Drupal Database Settings {:#db-settings}

You will need to know the database settings for your Drupal installation prior to running the CiviCRM installer: You can look up these values in your Drupal `settings.php` file (located by default in your `<drupal_root>/sites/default directory`) be looking for the following code:

```php
$databases = array (
  'default' =>
  array (
    'default' =>
    array (
      'database' => 'drupal',
      'username' => 'dbuser',
      'password' => 'dbpassword',
      'host' => 'localhost',
      'port' => '',
      'driver' => 'mysql',
      'prefix' => '',
    ),
  ),
);
```

In the above example:

| Setting                | Value      |
| ---------------------- | ---------- |
| Database Server        | localhost  |
| Drupal Database Name   | drupal     |
| Database User Name     | dbuser     |
| Database User Password | dbpassword |

<a name="directory"></a><!-- old anchor -->
## Get the code {:#download}

First, download CiviCRM and install the files like you would any other module:

* As administrator in Drupal, go to Modules, click on **Install New Module** , and it will ask you to fill in the URL of the module. It will then fetch it and install it for you. You will need to enable the **Update Manager** module in the Drupal modules page to see the **Install New Module** link.
* Alternatively, you can upload and extract the file in `<DRUPAL ROOT>/sites/all/modules`

The result will be that the CiviCRM directory will be at `<DRUPAL ROOT>/sites/all/modules/civicrm`

The most up-to-date version of CiviCRM is always available from the [CiviCRM website](https://civicrm.org/download)

**Do NOT** activate the module yet - that will happen automatically when you run the installer.

## Get the translations {:#i18n}

The basic CiviCRM release includes support for US English (`en_US`). To use another language or dialect, please [download and extract the translation files](../general/i18n_l10n.md).

## Run the Installer {:#installer}

The installer will verify that you've downloaded the correct version of CiviCRM, and will check your server environment to make sure it meets CiviCRM requirements. It will then create and populate a database for CiviCRM as well as create your CiviCRM settings file (civicrm.settings.php).

* Login to your Drupal site with Administrator level permissions.
* Point your web browser to the following URL:

    `https://example.org/sites/all/modules/civicrm/install/index.php`

* You should see the **CiviCRM Installer** screen.
    * Initially, you will see a red bar with the message "These database details don't appear to be correct." This is expected as you haven't entered your database settings yet.
    * If you see other errors, check the **Requirements** details at the bottom of the page for more information. You will need to correct any issues before continuing.
* Fill in the CiviCRM Database Settings.

    !!! tip "Where Should I Store CiviCRM Data?"
        CiviCRM may be configured to use your existing Drupal database, or a separate (new) database. Using a separate database is generally preferred - as it makes backups and upgrades easier. The installer will create a new database for CiviCRM automatically if you enter a database name that doesn't already exist on your database server AND the database user you enter has permission to create databases. In case the installer does not automatically create a new database, simply create a new one following the same process as creating a new database for Drupal. Note that if you plan to use the Drupal Views module to display CiviCRM data within your Drupal pages, and if you are going to use separate databases for Drupal and CiviCRM, you need to ensure that your Drupal database user has `SELECT` permissions for your CiviCRM database.

* Fill in the Drupal Database Settings for your existing Drupal database (as noted [above](#db-settings)).

    !!! check "Loading Sample Data"
        The Installer includes an option to load a set of sample contact, group, and relationship data by default. Sample data can provide a useful head-start in learning to use CiviCRM. However, if you do NOT want the sample data to be loaded, just uncheck **Load sample data** under **Other Settings**.

* Select the appropriate language for the base installation. You will be able to add other languages after the installation for multi-lingual sites.
* Click the **Check Requirements and Install CiviCRM** button.
    * The installer will configure your databases, create the settings file and redirect you to your Drupal Home page.
    * If you still see a red bar with the message "These database details don't appear to be correct." - check the Database Details section below your settings for specific errors and problems. Once you correct these problems, click "Recheck requirements" to verify your settings before continuing.
    * If you are on a Windows machine and get the message:
      > "The user account used by your web-server needs to be granted write access to the following directory in order to configure the CiviCRM settings file: `C:<DRUPAL ROOT>/sites/default/`"  
      even after changing directory permission in Explorer, see [the permissions page](../general/permissions.md) for instructions on how to set permissions on Linux, MacOS and Windows.
    * Once you see the green "You're ready to install!" message - you can click **Check Requirements and Install CiviCRM**

## Review the permissions {:#permissions}

!!! check ""
    Note that Drupal tries to create the `/files/` directory (and make it writeable), but only when saving `admin/settings`. Same holds for `/temp` directory, and a `/uploads/` directory in the CiviCRM module root. On a brand-new Drupal install, this directory may be missing. Even on an existing installation, if file permissions are not set properly, the directory may be missing. If enabling the **CiviCRM** module generates errors regarding the files directory, you must create it (writeable) manually.

* Go to the CiviCRM dashboard to see the CiviCRM menus:
`https://example.org/civicrm` (or `https://example.org/index.php?q=civicrm` if you don't have Clean URLs enabled)

* Go to **Administer » User management » Permissions**

* Verify that the Roles that you want to have access to CiviCRM have the appropriate permissions checked. CiviCRM is installed with a number of fixed permissions (such as "edit contacts" and "administer CiviCRM").
<!-- markdownlint-disable MD046 -->
!!! tip "Permissions for the Anonymous Role"
    Many sites want anonymous visitors to have access to certain CiviCRM functionality. These permissions are enabled during installation for the Anonymous role. You should review them and modify if needed based on your requirements:

    * **access all custom data** : If you plan on collecting "custom" data from visitors in standalone forms or as they make a contribution - enable this permission.
    * **access CiviMail subscribe/unsubscribe pages** : If you are planning on using CiviMail, enable this permission to allow anonymous users to subscribe and unsubscribe from mailing lists via the web.
    * **access uploaded files** : If you plan on allowing visitors to upload or view photos or other files - enable this permission.
    * **make online contributions** : If you plan on soliciting online contributions from visitors, enable this permission for the "anonymous" role.
    * **profile listings and forms** : If you plan on collecting name and address or other information from visitors, enable this permission for the "anonymous" role.
    * **view event info** and **register for events** : If you plan to use CiviEvent and want to allow un-authenticated visitors to view event information and register for events online - enable these permissions for the "anonymous" role.
    * **view event participants** : Enable this permission to allow anonymous users to access participant listing pages for events.
<!-- markdownlint-enable MD046 -->
## Create CiviCRM Contacts for Existing Drupal Users {:#contacts-users}

Once installed, CiviCRM keeps your Drupal Users synchronized with corresponding CiviCRM contact records. The 'rule' is that there will be a matched contact record for each Drupal user record. Conversely, only contacts who are authenticated users of your site will have corresponding Drupal user records.

When CiviCRM is installed on top of an existing Drupal site, a special CiviCRM Administrative feature allows you to automatically create CiviCRM contacts for all existing Drupal users:

* Login to your Drupal site with an administrator-level login
* Click the **CiviCRM** link in the main navigation block
* If your Drupal site makes use of the `db_prefix` setting (in `settings.php`), in the top bar click **Administer » System Settings » CMS Database Integration** , and update the box for the Drupal Users Table Name so that it includes the prefix.
* Click **Administer** in the menu bar
* Click **Users and Permissions** from the drop-down menu, then select **Synchronize Users to Contacts**

## Review the Configuration Checklist {:#checklist}

The **Configuration Checklist** provides a convenient way to work through the settings that need to be reviewed and configured for a new site. You can link to this checklist from the installation success page AND you can visit it at any time from **Administer** » **Administration Console** » **Configuration Checklist**.

## Test-drive CiviCRM {:#test-drive}

There should now be a **CiviCRM** link in your Drupal menu. Click that link and the CiviCRM Menu, Shortcuts, Search and New Individual Blocks should appear. You can now explore CiviCRM end-user features and begin configuring CiviCRM for your site/organization needs.

## Using Encryption with MySQL

If your MySQL database is hosted on a different machine than your web server, or if your host requires it, you can use TLS to encrypt the connection between the database and the web server.

Full instructions on installing drupal are out of scope for this guide, but one method is to install into a test database first without MySQL encryption and then move the database to the live server and update settings.php to enable MySQL encryption.

See [TLS for MySQL](/general/mysql_tls/) for introductory concepts and the settings for the CiviCRM database. For the Drupal database you have several options for updating settings.php:

1. The simplest, which doesn't require a client certificate, but doesn't verify the server certificate.

    ``` php
    $databases = array (
      'default' =>
      array (
        'default' =>
        array (
          'database' => 'drupal',
          'username' => 'dbuser',
          'password' => 'dbpassword',
          'host' => 'db435.examplehost.com',
          'port' => '',
          'driver' => 'mysql',
          'prefix' => '',
          'pdo' => array(
              PDO::MYSQL_ATTR_SSL_CA => TRUE,
              PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT => FALSE,
          ),
        ),
      ),
    );
    ```

2. Verifies the server certificate, and doesn't require a client certificate.

    !!! warning "Host name must match certificate name"
        Note that the DATABASE SERVER certificate would have to have a CN (common name field) that matches exactly the `host` you are using in `$databases['default']['default']['host']`. So if the host is `db435.examplehost.com`, then that must be the name on the SERVER certificate.

    ``` php
    'pdo' => array(
        // A certificate authority bundle.
        // If you are using a self-signed server certificate in a development
        // or testing environment, then this would be the same as the server
        // certificate.
        PDO::MYSQL_ATTR_SSL_CA => '/path/to/ca.crt',
    ),
    ```

3. Client certificate/key pair (not self-signed), and do not verify the server certificate.

    ``` php
    'pdo' => array(
        PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT => FALSE,
        PDO::MYSQL_ATTR_SSL_KEY => '/path/to/your.key',
        PDO::MYSQL_ATTR_SSL_CERT => '/path/to/your.crt',
    ),
    ```

4. Client certificate/key pair (could be self-signed), and verify the server certificate.

    ``` php
    'pdo' => array(
        // The SSL_CA can be the same as your.crt if self-signed, but note
        // that it would also have to be a certificate authority for the
        // server certificate. Self-signed would only be for local
        // development testing.
        PDO::MYSQL_ATTR_SSL_CA => '/path/to/ca.crt',
        PDO::MYSQL_ATTR_SSL_KEY => '/path/to/your.key',
        PDO::MYSQL_ATTR_SSL_CERT => '/path/to/your.crt',
    ),
    ```

## Trouble-shooting Resources {:#troubleshooting}

* Review the [Troubleshooting](../general/troubleshooting.md) page for help with problems you may encounter during the installation.
* To check compatibility with other Drupal modules see: [Drupal modules incompatible with CiviCRM](https://docs.civicrm.org/sysadmin/en/latest/integration/drupal/incompatibilities/)
