!!! tldr "About this document"

    This guide covers standard installation of CiviCRM on an existing Drupal 8 site. It assumes that you previously completed these tasks:

    1. [Install Drupal 8](https://www.drupal.org/docs/8/install), and...
    1. [Review the CiviCRM requirements](../general/requirements.md)

!!! tldr "Similar alternatives"

    If you plan to develop patches for CiviCRM on Drupal 8, then please read the [Developer Guide](https://docs.civicrm.org/dev/en/latest) for information about [Buildkit](https://docs.civicrm.org/dev/en/latest/tools/buildkit/) and [civibuild](https://docs.civicrm.org/dev/en/latest/tools/civibuild/).

!!! warning "Composer install required!"
    This guide will assume that you have installed Drupal 8 using composer. At this time manual installation of Drupal 8 using zip or tarball install methods is not supported.

<a name="downloading"></a><!-- old anchor -->
## Get the code {:#download}

To download CiviCRM on a Drupal 8 site we'll need to ask [Composer](https://www.getcomposer.org) to `require` the CiviCRM libraries. We do this by requiring the `civicrm-core`, `civicrm-drupal-8`, `civicrm-packages` and `civicrm-asset-plugin` libraries.

<!-- markdownlint-disable MD046 -->
!!! tip "Versions!"
    It is **strongly** recommended that you provide a version when requiring these libraries, as such our example command below will include, as an example, the version `5.27.2`. Note that installing ESR versions onto CiviCRM has not been tested at this time and as such no instructions for doing so are provided.

    Failure to provide a version will most likely result in you installing the `dev-master` version which is bleeding-edge code and may result in an unstable site/setup!

!!! tip "Composer running out of memory?"
    You may need to [increase composer's memory limit](https://getcomposer.org/doc/articles/troubleshooting.md#memory-limit-errors) in order to avoid out of memory errors.

    Best practice is to use `composer require` locally or in dev/test and then deploy your `composer.lock` to staging and use `composer install` which requires less memory and implements the changes you've tested and committed to your repo!
<!-- markdownlint-enable MD046 -->

### Expert Mode - Just the command

To require the CiviCRM libraries on a Drupal 8 site you can use the following one-line command:

??? note "Enabling Patching for composer libraries"
    You should make sure that before running this line that you either modify the composer.json file to include the parameter `"enable-patching": true` in the extra section or run `composer config 'extra.enable-patching' true` as per the [composer-patches documentation](https://github.com/cweagans/composer-patches#allowing-patches-to-be-applied-from-dependencies). This isn't necessary immediately but from CiviCRM 5.30 it will be required for CiviCRM to function correctly.

* `composer require civicrm/civicrm-asset-plugin:'~1.0.0' civicrm/civicrm-{core,packages,drupal-8}:'~5.29'`

### Guided Mode - More context and information

!!! tip "Location, Location... Location"
    You should always run `composer` commands from the top-level folder above the web and vendor folders, where in the same place as your `composer.json` file.

??? note "Enabling Patching for composer libraries"
    You should start by ensuring that patching is enabled for dependencies by altering composer.json and adding `"enable-patching": true` in the extra section or run `composer config 'extra.enable-patching' true` as per the [composer-patches documentation](https://github.com/cweagans/composer-patches#allowing-patches-to-be-applied-from-dependencies). This isn't necessary immediately but from CiviCRM 5.30 it will be required for CiviCRM to function correctly.

You can also install CiviCRM by running these commands separately, this is what that looks like, along with a brief explanation of what each step is doing:

1. Require the CiviCRM composer asset plugin which helps build a predictable structure for your CiviCRM codebase: `composer require civicrm/civicrm-asset-plugin:'~1.0.0'`
1. Require the CiviCRM core code: `composer require civicrm/civicrm-core:'~5.29'`
1. Require the CiviCRM third-party packages library: `composer require civicrm/civicrm-packages:'~5.29'`
1. Require the CiviCRM Drupal 8 integration code: `composer require civicrm/civicrm-drupal-8:'~5.29'`

!!! note "pear/exception conflict"
    If you get an error message from composer when trying to install CiviCRM that there is a conflict in requirements for the package pear/exception. Then run the following command: `composer require pear/pear_exception:'1.0.1 as 1.0.0'` before trying to run the command in step 3 above.

Optionally you can also require the [`cv`](https://github.com/civicrm/cv) command-line helper/interface for CiviCRM with:

!!! note ""
    **Composer installs of cv are currently broken** for now use the [manual install steps](https://github.com/civicrm/cv#download).

* `composer require civicrm/cv` - This will place the cv binary in `./vendor/bin/cv` relative to your `composer.json` file.

## Get the translations {:#i18n}

!!! warning "I18n & L10n on Drupal 8"
    If installing with the GUI it is currently only possible to install CiviCRM in English (US) on Drupal 8. Adding the language files involves breaking with Composer best practices by writing the contents of the `civicrm-l10n` tarball into `vendor/civicrm/civicrm-core` or configuring the `civicrm.l10n` directory path after you install and placing the contents of the `civicrm-l10n` tarball into the configured directory.

!!! error "Here Be Dragons..."
    The following steps are provided as an example - they are not supported or widely tested and may leave your site in a broken state. You use them at your own risk. No. Seriously...

    You will also have to repeat these steps **every** time you upgrade CiviCRM.

The warnings above notwithstanding to install CiviCRM on Drupal 8 requires the following additional steps to prepare:

1. Add [`cv`](https://github.com/civicrm/cv) to your Drupal 8 Site with: `composer require civicrm/cv` (**composer installs of cv are currently broken** for now use the [manual install steps](https://github.com/civicrm/cv#download).)
1. Grab the localisation (*l10n*) files and unpack the `l10n` and `sql` subfolders into `vendor/civicrm/civicrm-core/`
    * You'll find the l10n files on the [CiviCRM Download](https://civicrm.org/download) page or from `https://download.civicrm.org/civicrm-VERSION-l10n.tar.gz` where `VERSION` is a recent version of CiviCRM.

    **Example:**

    <!-- markdownlint-disable MD046 -->
    ``` bash
    wget https://download.civicrm.org/civicrm-5.27.2-l10n.tar.gz
    tar -zxvf civicrm-5.27.2-l10n.tar.gz
    cd civicrm/
    cp -R l10n/ ../vendor/civicrm/civicrm-core/
    cp -R sql/ ../vendor/civicrm/civicrm-core/
    cd ..
    rm -rf civicrm/
    ```
    <!-- markdownlint-enable MD046 -->

1. If you've done this correctly, you should end up with `vendor/civicrm/civicrm-core/l10n` and `vendor/civicrm/civicrm-core/sql/`
    * You can remove any languages you don't need by deleting them before copying the `l10n` and `sql` folders.

Now we move onto [Installing CiviCRM - Command line install](#installing-commandline)

<a name="installing"></a>
## Run the installer {:#installer}

The installer prepares the database and configuration files.  You may run the installer through the web interface (which is simpler) or the command-line interface (which has more options).

### Web installer {:#installing-gui}

<!-- markdownlint-disable MD046 -->
!!! warning "Write permissions"
    It is critical that your web-server user is able to write to the `web/sites/default/` directory in order to create `civicrm.settings.php` and that you have an appropriate value for execution time(s) and memory limit(s) as any interruption to the installer can (and will) result in an unusable install and require remedial steps to correct or a full reinstall! By default on Drupal 8.8+ this directory path is not writable by default, before installing you should ensure you grant write access to your web server user. With, e.g: `sudo chmod u+w web/sites/default`.

    For multisite installations you'll also need to ensure your web server user has write access to additional sites e.g: `sudo chmod u+w web/sites/site2.example.org`.
<!-- markdownlint-enable MD046 -->

* Login to your Drupal site with Administrator level permissions.
* Proceed to **Manage >> Extend** or point your web browser to the following URL:

    `https://example.org/admin/modules/`

* Currently there is no interactive installer for CiviCRM on Drupal 8 and enabling the module in Drupal 8 will install CiviCRM into your existing Drupal 8 database.

    !!! tip "Where Should I Store CiviCRM Data?"
        [GUI installs](#installing-gui) of CiviCRM on Drupal 8 can only install into your existing Drupal 8 database. However using a separate database is generally preferred - as it makes backups and upgrades easier. If you want to install via the GUI **and** use a separate CiviCRM database you'd need to create the CiviCRM database manually and move the `civicrm_` tables into the new CiviCRM database, then update `civicrm.settings.php` with the new database details. If you want to install directly into a separate database see the [command line install](#installing-commandline) instructions.

### CLI installer {:installing-commandline}

<!-- markdownlint-disable MD046 -->
!!! note "Prerequisites"
    The steps below assume that you have the prerequisites for a command line install, namely cv or some other tested command-line installer for Drupal 8 (**Hint:** There's only one of those at the moment and it's cv)

!!! warning "Write permissions"
    It is critical that your web-server user is able to write to the `web/sites/default/` directory in order to create `civicrm.settings.php` and that you have an appropriate value for execution time(s) and memory limit(s) as any interruption to the installer can (and will) result in an unusable install and require remedial steps to correct or a full reinstall! By default on Drupal 8.8+ this directory path is not writable by default, before installing you should ensure you grant write access to your web server user. With, e.g: `sudo chmod u+w web/sites/default`.

    For multisite installations you'll also need to ensure your web server user has write access to additional sites e.g: `sudo chmod u+w web/sites/site2.example.org`.
<!-- markdownlint-enable MD046 -->

Make sure you're in the root directory for your Drupal 8 / CiviCRM site (the same folder/directory that holds `composer.json`).

The essence of a cv install command looks like this `cv core:install -vv --cms-base-url="https://urltocms.example.org" --db="mysql://database:details@go/here:3306" --lang="en_GB"` there are many parameters available. <!-- META:To-Do Document cv usage -->

For our CiviCRM install on Drupal 8 we want a command that looks like this:

<!-- markdownlint-disable MD046 -->
``` bash
cv core:install -vv --db="DATABASE" --cms-base-url="URL" --lang="LANG"
```
<!-- markdownlint-enable MD046 -->

Replace `cv` with the path to the cv phar on your system if applicable. The parts in `CAPS` above will need to be replaced with your site-specific information as follows:

* `DATABASE` (required) is the [DSN](https://en.wikipedia.org/wiki/Data_source_name) for the database where you want CiviCRM installed.
* `URL` (required) is the canonical URL to the root of your CMS site.
* `LANG` (optional) is one of the supported languages for CiviCRM - it **must** exist in your `l10n` folder.

#### Installing Sample Data {:#sampledata}

Currently you can only install sample data using the command line and the `-m` flag to set the `loadGenerated` option. Example:

<!-- markdownlint-disable MD046 -->
``` bash
cv core:install -vv --cms-base-url="URL" -m loadGenerated=1
```
<!-- markdownlint-enable MD046 -->

## Review the permissions {:#permissions}

!!! note ""
    Drupal will create the `/files/` directory (and make it writeable), but only when saving `admin/settings`. Same holds for `/temp` directory, and a `/uploads/` directory in the CiviCRM module root. On a brand-new Drupal install, this directory may be missing. Even on an existing installation, if file permissions are not set properly, the directory may be missing. If enabling the **CiviCRM** module generates errors regarding the files directory, you must create it (writeable) manually.

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

## Synchronize the users {:#contacts-users}

Once installed, CiviCRM keeps your Drupal Users synchronized with corresponding CiviCRM contact records. The 'rule' is that there will be a matched contact record for each Drupal user record. Conversely, only contacts who are authenticated users of your site will have corresponding Drupal user records.

When CiviCRM is installed on top of an existing Drupal site, a special CiviCRM Administrative feature allows you to automatically create CiviCRM contacts for all existing Drupal users:

* Login to your Drupal site with an administrator-level login
* Click the **CiviCRM** link in the main navigation block
* If your Drupal site makes use of the `db_prefix` setting (in `settings.php`), in the top bar click **Administer » System Settings » CMS Database Integration** , and update the box for the Drupal Users Table Name so that it includes the prefix.
* Click **Administer** in the menu bar
* Click **Users and Permissions** from the drop-down menu, then select **Synchronize Users to Contacts**

## Review the checklist {:#checklist}

The **Configuration Checklist** provides a convenient way to work through the settings that need to be reviewed and configured for a new site. You can link to this checklist from the installation success page and you can visit it at any time from **Administer** » **Administration Console** » **Configuration Checklist**.

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

## D8 <-> CiviCRM Integration modules {:#integration-modules}

### Webform CiviCRM module

* [Project page on Drupal.org](https://www.drupal.org/project/webform_civicrm)
* [Documentation on CiviCRM.org](https://docs.civicrm.org/webform-civicrm/en/latest)

### CiviCRM Entity module

* [Project page on Drupal.org](https://www.drupal.org/project/civicrm_entity)

## Troubleshooting {:#troubleshooting}

* Review the [Troubleshooting](../general/troubleshooting.md) page for help with problems you may encounter during the installation.
