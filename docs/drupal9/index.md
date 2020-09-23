!!! warning "Experimental documentation"

    This document discusses a new deployment environment. It is closesly based on the Drupal 8
    document, but it has not been carefully reviewed/tested. Consequently, it is not linked in
    the main menu.

???+ tldr "About this document"

    This guide covers standard installation of CiviCRM on an existing Drupal 9 site. It assumes that you previously completed these tasks:

    1. Install Drupal 9, and...
    1. [Review the CiviCRM requirements](../general/requirements.md)

??? tldr "Alternative: Civibuild for developers"

    If you plan to develop patches for CiviCRM on Drupal 9, then please read the [Developer Guide](https://docs.civicrm.org/dev/en/latest) for information about [Buildkit](https://docs.civicrm.org/dev/en/latest/tools/buildkit/) and [civibuild](https://docs.civicrm.org/dev/en/latest/tools/civibuild/).

<a name="downloading"></a><!-- old anchor -->
## Get the code {:#download}

Drupal 9 (D9) sites are typically administered with [Composer](https://www.getcomposer.org). Composer is a *dependency management* tool which can add, upgrade, and remove software *packages* for your site.

CiviCRM is published as a suite of related packages. Our goal is to use Composer to add CiviCRM's packages to the D9 site.

If you do not work regularly with D9+Composer, then you should take a refresher before installing CiviCRM.

??? example "Quick and dirty introduction to D9 with `composer`"

    Composer requires shell access to the D9 site. It defines a command `composer`.

    Many D9 sites are initialized via `composer`, which means that `composer` is already available.  This
    can be confirmed in the shell by running `composer --version`:

    ```
    $ composer --version
    Composer version 1.10.13 2020-09-09 11:46:34
    ```

    It is possible that your system does not have `composer` -- for example, if you used a tar-based installation of D9,
    then you may never have needed `composer` before.

    If `composer` is missing, then you must [download and install it](https://getcomposer.org/download/) first.
    Additionally, you probably need to [set composer's memory limit](https://getcomposer.org/doc/articles/troubleshooting.md#memory-limit-errors)
    high enough for D9.

    Once you have `composer`, you need to navigate to the composer-root. You can recognize it by the following:

    * It has the files `composer.json` and `composer.lock`.
    * It has a subfolder `vendor/`.
    * It usually has a subfolder `web/` (the web-root); alternatively, it may *be* the web-root.

    <!-- For D9, do we still need the caveats about web-root? Are we sure everyone on D9 has web/? -->

    A typical file-hierarchy might look like:

    ```
    /var/www/d9.example.org/                Composer-root
    /var/www/d9.example.org/composer.json   Composer-configuration
    /var/www/d9.example.org/composer.lock   Composer-configuration
    /var/www/d9.example.org/web/            Web-root (usually)
    /var/www/d9.example.org/vendor/         Downloaded packages
    ```

    To work with `composer` and D9, you must open a shell navigate to the composer-root, e.g.

    ```
    cd /var/www/d9.example.org
    ```

If `composer` is properly installed, then these example commands will add CiviCRM to D9:

```
cd /var/www/d9.example.org
composer config extra.enable-patching true
composer require civicrm/civicrm-asset-plugin:'~1.0'
composer require civicrm/civicrm-{core,packages,drupal-8}:'~5.29'
```

You should adjust the example path (`/var/www/d9.example.org`) and the example version (`~5.29`) as needed.

If you'd like more details to understand these commands or common errors, then please drill-down below.

??? info "More detail: Enable patching"

    A handful of packages used by CiviCRM require extra patch-files.

    This is possible with the popular [cweagans/composer-patches](https://github.com/cweagans/composer-patches)
    plugin. However, you must [opt-in to enable it](https://github.com/cweagans/composer-patches#allowing-patches-to-be-applied-from-dependencies).

??? info "More detail: Required packages"

    | Package | Description |
    | -- | -- |
    | `civicrm/civicrm-asset-plugin` | A tool which automatically copies JS+CSS assets from CiviCRM to D9's `web/` folder |
    | `civicrm/civicrm-core` | The primary CiviCRM codebase |
    | `civicrm/civicrm-drupal-8` | The integration module for CiviCRM and D8/D9 |
    | `civicrm/civicrm-packages` | A collection of third-party/legacy packages used by CiviCRM |

??? info "More detail: Version constraints"

    The primary CiviCRM packages (`civicrm-core`, `civicrm-drupal-8`, `civicrm-packages`) have *synchronized*
    versions. If one package is installed with v5.30.1, then the others should also be v5.30.1.

    The following expression references the three packages and applies the same version-constraint to each:

    ```
    civicrm/civicrm-{core,packages,drupal-8}:'~5.29'
    ```

    The expression `~5.29` is a version-constraint.  It means that composer will install *approximately* v`5.29`.  It may
    install a newer patch-release (e.g.  `5.29.1`) or a newer minor-release (e.g.  `5.31.0`).  However, it will avoid
    major-releases (e.g. `6.0.0`).

    Many `composer` tutorials rely on `composer` to automatically choose package-versions.
    This is not recommended for CiviCRM/D9. Instead, package versioning should be explicit to ensure that:

    1. CiviCRM versions remain synchronized.
    2. CiviCRM stable releases are preferred over developmental releases.

       <!-- honestly, that second thing is weird to me.  if people get dev releases unintentionally, then they've
        probably misconfigured/misunderstood their `composer.json`.  but given how consultancies blend upstream/public
        pkgs and inhouse/private pkgs, and given how inhouse pkgs tend to have lax versioning, i can see how there'd
        be pressure on D9 site-builders to make the configuration promiscuous -->

??? question "How to resolve conflicts in `pear/exception`?"

    <!-- Is this still needed in D9? I feel like they fixed in later D8.x templates. -->

    In some configurations, you may see an error message about the package `pear/exception`. This is
    because some packages use `pear/exception` -- but they have been overly specific about the required
    version. (To wit: they require version `1.0.0` when version `1.0.1` will also work.)

    To resolve this error, you can install v1.0.1 and *pretend* that it is v1.0.0:

    ```
    composer require pear/pear_exception:'1.0.1 as 1.0.0'
    ```

    After fixing this, you may continue the regular installation commands.

<!--

Optionally you can also require the [`cv`](https://github.com/civicrm/cv) command-line helper/interface for CiviCRM with:

!!! note ""
    **Composer installs of cv are currently broken** for now use the [manual install steps](https://github.com/civicrm/cv#download).

* `composer require civicrm/cv` - This will place the cv binary in `./vendor/bin/cv` relative to your `composer.json` file.

-->

## Get the translations {:#i18n}

!!! warning "I18n & L10n on Drupal 9"
    If installing with the GUI it is currently only possible to install CiviCRM in English (US) on Drupal 9. Adding the language files involves breaking with Composer best practices by writing the contents of the `civicrm-l10n` tarball into `vendor/civicrm/civicrm-core` or configuring the `civicrm.l10n` directory path after you install and placing the contents of the `civicrm-l10n` tarball into the configured directory.

!!! error "Here Be Dragons..."
    The following steps are provided as an example - they are not supported or widely tested and may leave your site in a broken state. You use them at your own risk. No. Seriously...

    You will also have to repeat these steps **every** time you upgrade CiviCRM.

The warnings above notwithstanding to install CiviCRM on Drupal 9 requires the following additional steps to prepare:

1. Add [`cv`](https://github.com/civicrm/cv) to your Drupal 9 Site with: `composer require civicrm/cv` (**composer installs of cv are currently broken** for now use the [manual install steps](https://github.com/civicrm/cv#download).)
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

<a name="installing"></a><a name="installing-gui"></a><a name="installing-commandline"></a>
## Run the installer {:#installer}

The installer verifies requirements, prepares the database, and initializes the configuration file. You may run the installer through the web interface (*which is simpler*) or the command-line interface (*which has more options*).

??? example "Run installer via Drupal 9 web UI"

    ??? warning "Installation options are very limited"

        Currently there is no interactive installer for CiviCRM on Drupal 9, so the installer uses a firm set of defaults, e.g.

        * *English Language Data*: It only installs data for US English. It cannot install data for other languages.
        * *Shared Database*: It only uses the shared CMS database. It cannot use [a separate MySQL database for CiviCRM](../general/requirements.md#mysql-connection).
        * *No Sample Data*: It only installs an empty, baseline dataset. It cannot install sample data.

        If you need to configure any of these options, then use the command-line installer.

    ??? warning "Write permissions"
        It is critical that your web-server user is able to write to the `web/sites/default/` directory in order to create `civicrm.settings.php` and that you have an appropriate value for execution time(s) and memory limit(s) as any interruption to the installer can (and will) result in an unusable install and require remedial steps to correct or a full reinstall! By default on Drupal 8.8+ this directory path is not writable by default, before installing you should ensure you grant write access to your web server user. With, e.g: `sudo chmod u+w web/sites/default`.

        For multisite installations you'll also need to ensure your web server user has write access to additional sites e.g: `sudo chmod u+w web/sites/site2.example.org`.

    1. Login to your Drupal site with *administrator* permissions.
    2. Navigate to **Manage >> Extend** or point your web browser to the following URL:

        `https://example.org/admin/modules/`

    3. Find "CiviCRM" and enable it.
    4. At the bottom, click "Save Configuration". (Note: This may take a few moments to execute.)

??? example "Run installer via command-line"

    CiviCRM has a command-line administration tool, `cv`, which can perform installation. For details, see [command-line installer](../general/cli-cv.md).

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

## Addenda

### TLS for MySQL

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

### Integration modules {:#integration-modules}

__Webform CiviCRM module__

* [Project page on Drupal.org](https://www.drupal.org/project/webform_civicrm)
* [Documentation on CiviCRM.org](https://docs.civicrm.org/webform-civicrm/en/latest)

__CiviCRM Entity module__

* [Project page on Drupal.org](https://www.drupal.org/project/civicrm_entity)

### Troubleshooting {:#troubleshooting}

* Review the [Troubleshooting](../general/troubleshooting.md) page for help with problems you may encounter during the installation.
