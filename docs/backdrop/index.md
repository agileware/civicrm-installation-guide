???+ tldr "About this document"

    This guide covers standard installation of CiviCRM on an existing Backdrop site. It assumes that you previously completed these tasks:

    1. [Install Backdrop](https://backdropcms.org/installation), and...
    1. [Review the CiviCRM requirements](../general/requirements.md)

??? tldr "Alternative: Civibuild for developers"

    If you plan to develop patches for CiviCRM on WordPress, then please read the [Developer Guide](https://docs.civicrm.org/dev/en/latest) for information about [Buildkit](https://docs.civicrm.org/dev/en/latest/tools/buildkit/) and [civibuild](https://docs.civicrm.org/dev/en/latest/tools/civibuild/).

<a name="directory"></a><!-- old anchor -->
## Get the code {:#download}

The [CiviCRM download page](https://civicrm.org/download) provides a pre-built archive for use with Backdrop. A
typical archive file is ~20mb and looks like `civicrm-X.Y.Z-backdrop.tar.gz`. It contains a folder named `civicrm/`.

In Backdrop, there is a folder for storing add-on modules: `BACKDROP_ROOT/modules`.

Our goal is to download the archive and extract into this folder. Here are a few example ways to download and extract:

??? example "Download via web-browser"

    This method does not require shell access on the server. You may adapt based your server's arrangement.

    * On the [CiviCRM download page](https://civicrm.org/download), click the link for CiviCRM / Backdrop. Save the file locally.
    * Extract the archive locally. For example, in a Linux workstation, one might say:
      ```bash
      cd ~/Downloads
      tar xvzf civicrm-X.Y.Z-backdrop.tar.gz
      ```
    * Observe the new folder `civicrm/`.
    * Using your favor file-transfer system (SSH/SFTP, `rsync`, `git`, `mv`, etc), transfer the `civicrm/` folder
      to `BACKDROP_ROOT/modules`.

??? example "Download via curl + tar"

    This method require shell access to the Backdrop server.

    * On the [CiviCRM download page](https://civicrm.org/download), note the current version.
    * In the shell, navigate to the Backdrop module folder (*adjust as appropriate*):
      ```bash
      cd BACKDROP_ROOT/modules
      ```
    * In the shell, run the `curl` and `tar` commands. Adjust the version `X.Y.Z` to the appropriate value.
      ```bash
      curl -L "https://download.civicrm.org/civicrm-X.Y.Z-backdrop.tar.gz" | tar xvz
      ```

??? example "Download via Backdrop web UI"

    This is the easiest way for a new administrator. However, it may not work in all deployments. For
    example, if the Backdrop server has a locked-down configuration, then it may encounter permission errors
    or timeouts. The only way to find out is to try.

    * On the [CiviCRM download page](https://civicrm.org/download), right-click the link for CiviCRM / Backdrop. Copy the URL.
    * Login to your Backdrop site with *administrator* permissions.
    * Navigate to **Functionality >> Install new modules**.
    * In the right hand bar, choose **Manual installation >> Install from a URL**.
    * Paste the URL
    * Click "Install"

    ??? warning "This may not work with CiviCRM 5.13-5.30"

        In CiviCRM versions 5.13 - 5.30, the prebuilt tarball included a symlink which is not accepted by
        the Backdrop installer. This should work again in 5.31.

        <!-- TODO: After 5.31 has been out for a while, we can move this option higher up and remove/downgrade the notice. -->

    ??? warning "The "Upload"/"Browse" option is unlikely to work."

        The **Install new module** page provides another option for uploading the archive from your local computer.
        In a default PHP configuration, the upload limits are too conservative to accept the CiviCRM archive.

In all cases, the final outcome should be the creation of a new folder

```
BACKDROP_ROOT/modules/civicrm
```


## Get the translations {:#i18n}

The basic CiviCRM release includes support for US English (`en_US`). To use another language or dialect, please [download and extract the translation files](../general/i18n_l10n.md).

## Run the installer {:#installer}

The installer verifies requirements, prepares the database, and initializes the configuration file. You may run the installer through the web interface (*which is simpler*) or the command-line interface (*which has more options*).

??? example "Run installer via Backdrop web UI"

    1. Enable the "CiviCRM" module
        * Login to your Backdrop site with *administrator* permissions.
        * Navigate to the "Functionality" page (`admin/modules`).
        * Find "CiviCRM" and enable it. At the bottom, click "Save Configuration".
    2. After enabling, the status message will display a link to "configure CiviCRM". Click it.

        ??? question "What if I didn't notice the link?"
            Use the URL bar. Navigate to the `civicrm` page (e.g. `https://example.com/civicrm`).

    3. The CiviCRM installer will open.
        * *If there are unmet requirements*, the installer will list them. Consult the [Requirements](../general/requirements.md) documentation for additional advice.
        * *If all the requirements are met*, proceed through the brief questionnaire.
        * *If you have [a separate MySQL database for CiviCRM](../general/requirements.md#mysql-connection)*, then locate "Environment: CiviCRM Database". Click the edit icon and enter the [database URL](../general/requirements.md#mysql-connection).
        * Finally, click "Install CiviCRM".

??? example "Run installer via command-line"

    CiviCRM has a command-line administration tool, `cv`, which can perform installation. For details, see [command-line installer](../general/cli-cv.md).

The installer will verify that you've downloaded the correct version of CiviCRM, and will check your server environment to make sure it meets CiviCRM requirements. It will then create and populate a database for CiviCRM as well as create your CiviCRM settings file (civicrm.settings.php).

## Review the permissions {:#permissions}

!!! check ""
    Note that Backdrop tries to create the `/files/` directory (and make it writeable), but only when saving `admin/settings`. Same holds for `/temp` directory, and a `/uploads/` directory in the CiviCRM module root. On a brand-new Backdrop install, this directory may be missing. Even on an existing installation, if file permissions are not set properly, the directory may be missing. If enabling the **CiviCRM** module generates errors regarding the files directory, you must create it (writeable) manually.

* Go to **Administer » Configuration » User accounts » Permissions**

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

Once installed, CiviCRM keeps your Backdrop Users synchronized with corresponding CiviCRM contact records. The 'rule' is that there will be a matched contact record for each Backdrop user record. Conversely, only contacts who are authenticated users of your site will have corresponding Backdrop user records.

When CiviCRM is installed on top of an existing Backdrop site, a special CiviCRM Administrative feature allows you to automatically create CiviCRM contacts for all existing Backdrop users:

* Login to your Backdrop site with an administrator-level login
* Click the **CiviCRM** link in the main navigation block
* If your Backdrop site makes use of the `db_prefix` setting (in `settings.php`), in de top bar click **Administer » System Settings » CMS Database Integration** , and update the box for the Backdrop Users Table Name so that it includes the prefix.
* Click **Administer** in the menu bar
* Click **Users and Permissions** from the drop-down menu, then select **Synchronize Users to Contacts**

## Review the checklist {:#checklist}

The **Configuration Checklist** provides a convenient way to work through the settings that need to be reviewed and configured for a new site. You can link to this checklist from the installation success page AND you can visit it at any time from **Administer** » **Administration Console** » **Configuration Checklist**.

## Test-drive CiviCRM {:#test-drive}

There should now be a **CiviCRM** link in your Backdrop menu. Click that link and the CiviCRM Menu, Shortcuts, Search and New Individual Blocks should appear. You can now explore CiviCRM end-user features and begin configuring CiviCRM for your site/organization needs.

## Addenda

### TLS for MySQL

If your MySQL database is hosted on a different machine than your web server, or if your host requires it, you can use TLS to encrypt the connection between the database and the web server.

Full instructions on installing drupal are out of scope for this guide, but one method is to install into a test database first without MySQL encryption and then move the database to the live server and update settings.php to enable MySQL encryption.

See [TLS for MySQL](../general/mysql_tls.md) for introductory concepts and the settings for the CiviCRM database. For the Drupal database you have several options for updating settings.php:

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

### Troubleshooting {:#troubleshooting}

* Review the [Troubleshooting](../general/troubleshooting.md) page for help with problems you may encounter during the installation.
