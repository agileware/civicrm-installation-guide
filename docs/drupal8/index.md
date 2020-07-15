## Scope of this guide and alternative installation methods

This guide covers standard installation of CiviCRM for production use. For installing a development environment, refer to the [section on Buildkit in the Developer Documentation](https://docs.civicrm.org/dev/en/latest/tools/buildkit/).

## Before installing

1. Ensure that your system meets the [requirements](../general/requirements.md).
1. Install Drupal 8 by referring to the [Drupal 8 Installation Guide](https://www.drupal.org/docs/8/install) if needed.

!!! warning "Composer install required!"
    This guide will assume that you have installed Drupal 8 using composer. At this time manual installation of Drupal 8 using zip or tarball install methods is not supported.

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

## Tell Drupal 8 where to find the CiviCRM Module {:#directory}

To download CiviCRM on a Drupal 8 site we'll need to ask [Composer](https://www.getcomposer.org) to `require` the CiviCRM libraries. We do this by requiring the `civicrm-core`, `civicrm-drupal-8`, `civicrm-packages` and `civicrm-asset-plugin` libraries.
<!-- markdownlint-disable MD046 -->
!!! tip "Versions!"
    It is **strongly** recommended that you provide a version when requiring these libraries, as such our example command below will include, as an example, the version `5.27.1`. Note that installing ESR versions onto CiviCRM has not been tested at this time and as such no instructions for doing so are provided.

    Failure to provide a version will most likely result in you installing the `dev-master` version which is bleeding-edge code and may result in an unstable site/setup!
<!-- markdownlint-enable MD046 -->
To require the CiviCRM libraries on a Drupal 8 site you can use the following one-line command:

* `composer require civicrm/civicrm-asset-plugin:'~1.0.0' civicrm/civicrm-{core,packages,drupal-8}:'~5.27.1'`

## Install localization files (only for non-English sites) {:#i18n}

!!! warning "I18n & L10n on Drupal 8"
    It's currently only possible to install CiviCRM in English (US) on Drupal 8 and adding the language files involves breaking with Composer best practices by writing the contents of the `civicrm-l10n` tarball into `vendor/civicrm/civicrm-core` or configuring the `civicrm.l10n` directory path after you install and placing the contents of the `civicrm-l10n` tarball into the configured directory.

## Run the Installer {:#installer}

!!! warning "Write permissions"
    It is critical that your web-server user is able to write to the `web/sites/default/` directory in order to create `civicrm.settings.php` and that you have an appropriate value for execution time(s) and memory limit(s) as any interruption to the installer can (and will) result in an unusable install and require remedial steps to correct or a full reinstall! By default on Drupal 8.8+ this directory path is not writable by default, before installing you should ensure you grant write access to your web server user. With, e.g: `sudo chmod u+w web/sites/default`.

* Login to your Drupal site with Administrator level permissions.
* Proceed to **Manage >> Extend** or point your web browser to the following URL:

    `https://example.org/admin/modules/`

* Currently there is no interactive installer for CiviCRM on Drupal 8 and enabling the module in Drupal 8 will install CiviCRM into your existing Drupal 8 database.

    !!! tip "Where Should I Store CiviCRM Data?"
        CiviCRM on Drupal 8 can only install into your existing Drupal 8 database. However using a separate database is generally preferred - as it makes backups and upgrades easier. If you want to use a separate CiviCRM database you'd need to create the CiviCRM database manually and move the `civicrm_` tables into the new CiviCRM database, then update `civicrm.settings.php` with the new database details.

## Review Permissions {:#permissions}

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

## D8/CiviCRM Integration modules {:#integration-modules}

Webform CiviCRM module
Project page: https://www.drupal.org/project/webform_civicrm
Documentation: https://docs.civicrm.org/webform-civicrm/en/latest

CiviCRM Entity module
Project page: https://www.drupal.org/project/civicrm_entity

## Trouble-shooting Resources {:#troubleshooting}

* Review the [Troubleshooting](../general/troubleshooting.md) page for help with problems you may encounter during the installation.
* To check compatibility with other Drupal modules see: [Drupal modules incompatible with CiviCRM](https://docs.civicrm.org/sysadmin/en/latest/integration/drupal/incompatibilities/)
