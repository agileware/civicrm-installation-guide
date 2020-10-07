<!-- markdownlint-disable MD046 -->

???+ tldr "About this document"

    This guide covers installation of CiviCRM on the command-line. It assumes that you previously completed these tasks:

    1. Install a supported CMS
    1. [Review the CiviCRM requirements](../general/requirements.md)
    1. Download the CiviCRM code. (See "*Get the code*" for [Backdrop](../backdrop/index.md#download), [Drupal 7](../drupal7/index.md#download), [Drupal 8](../drupal8/index.md#download), [Drupal 9](../drupal9/index.md#download), or [WordPress](../wordpress/index.md#download)).

??? tldr "Limitations"

    At time of writing, the CLI installer is primarily tested with Backdrop, Drupal 7/8/9, and WordPress.  It is primarily tested with
    single-site deployments. For other configurations (e.g. Joomla; e.g. multisite), it may require extra variables/options/patches.

<!-- markdownlint-enable MD046 -->

The installer verifies requirements, prepares the database, and initializes the configuration file.

Each Civi-CMS integration has an option to install through the web interface -- which is intended for new and casual administrators.

For more advanced administrators, there is a general-purpose command-line interface (CLI) for installation. This is useful for:

* Automating installation
* Accessing uncommon or hidden options
* Debugging or developing changes to the installer

??? tip "Debugging and development"

    If you are using the CLI for debugging or development, then the __CiviCRM Developer Guide__ has additional guidance.
    See [Setup Reference: Overview](https://docs.civicrm.org/dev/en/latest/framework/setup/) and
    [Setup Reference: Getting Started](https://docs.civicrm.org/dev/en/latest/framework/setup/getting-started/)

## Download "cv"

The CLI installer is part of [cv](https://github.com/civicrm/cv), a general-purpose tool for managing CiviCRM on the command-line.  `cv` is
distributed in PHAR format, which is a portable executable file for PHP.  It should run in most Unix-like environments that support PHP.

Simply download [cv.phar](https://download.civicrm.org/cv/cv.phar) and put it somewhere in the `PATH`. For example:

```bash
sudo curl -LsS https://download.civicrm.org/cv/cv.phar -o /usr/local/bin/cv
sudo chmod +x /usr/local/bin/cv
```

## Change directory

To run `cv`, you should navigate into the base folder of the CMS. For example:

```bash
cd /var/www/example.org
```

## Run the installer

A typical installation command will look like this:

```bash
cv core:install --cms-base-url="URL" --lang="LANG"
```

For example:

```
$ cv core:install --cms-base-url="https://example.org" --lang="fr_CA"

Found code for civicrm-core in /var/www/example.org/wp-content/plugins/civicrm/civicrm
Found code for civicrm-setup in /var/www/example.org/wp-content/plugins/civicrm/civicrm/setup
Creating file /var/www/example.org/wp-content/uploads/civicrm/civicrm.settings.php
Creating civicrm_* database tables in hydrawpcms_p7urq
```

There are several available options for the installer. Expand the topics below for more details.

<!-- FIXME: Options for multisite ... -->

??? info "Option: CMS Base URL"

    ```
    cv core:install ... --cms-base-url="https://example.org"
    ```

    The "CMS Base URL" is the public URL of the website. It is required for most CLI installations.

    Setting this option ensures that CiviCRM's background jobs (e.g. email-blasts and membership-reminders)
    can generate correct URLs.

??? info "Option: Database URL"

     ```
     cv core:install ... --db="mysql://user:pass@hostname:port/db"
     ```

     The "Database URL" points to the MySQL database where we will install CiviCRM.

     By default, CiviCRM will use the same database as the CMS. However, some administrators prefer to use a separate database.
     See [Requirements: MySQL: Connection](../general/requirements.md#mysql-connection) for more discussion.

??? info "Option: Language"

    ```
    cv core:install ... --lang=fr_CA
    ```

    The "Language" is the primary language used by the organization.

    Based on the language, the installer loads a set of *default configuration data*.  This includes common activity types ("Meeting" or
    "Phone Call"), common location types ("Home" or "Office"), and so on.

    __Note__: For a specific language to work, there *must* be corresponding data in the `l10n` folder.

??? info "Option: Sample data"

    ```
    cv core:install ... -m loadGenerated=1
    ```

    The sample data provides a number of fake, auto-generated records -- i.e.  sample contacts, contributions, activities, etc.  The sample
    data is useful for testing or reviewing CiviCRM.

    __Note__: At time of writing, the sample data is only supported in the default locale, `en_US`.

??? info "Option: Verbose output"

    ```bash
    ## Verbose
    cv core:install ... -v

    ## Very verbose
    cv core:install ... -vv
    ```

    The "verbose" option  provides more details about how the installer works. Enabling the verbose option helps one identify problems.

The installer may report a problem. Some common ones are:

??? warning "Problem: CMS Base URL"

    On most systems, the `--cms-base-url` cannot be detected automatically. You must explicitly set it.

??? warning "Problem: File write permission"

    The installer needs to create some files and folders. However, if the parent folder is locked-down, then you will need to relax permissions.

    For example: in a Drupal site, the installer creates a file `web/sites/<sitename>/civicrm.settings.php`. You may need to mark
    the parent folder (`web/sites/default` or `web/sites/example.org`) as writeable, e.g.:

    ```bash
    ## Grant write-access for the owner-user
    chmod 755 web/sites/default

    ## Grant write-access for all users
    chmod 777 web/sites/default
    ```

??? warning "Problem: PHP or MySQL configuration"

    The PHP or MySQL configuration may require an update - e.g. to expand the PHP memory limit, to enable a PHP extension, or to enlarge a MySQL buffer.
    Common issues are discussed in the [Requirements](requirements.md).

For common problems, you can simply fix the problem and re-run the installer.

For unusual problems, be sure to enable verbose output (`-v` or `-vv`). It may also help to [inspect the model, plugins, or requirements](https://docs.civicrm.org/dev/en/latest/framework/setup/getting-started/).

## Configure the site

After running `core:install`, you can login to the CMS, open CiviCRM, and configure additional details.  The [System Administrator
Guide](https://docs.civicrm.org/sysadmin/en/latest/) has more advice on how to configure CiviCRM.

If you are automating the installation, then you may wish to automate some common configuration steps, such as:

??? info "Task: Manage extensions"

    To view available extensions:

    ```bash
    cv ext:list
    ```

    To download extensions from the [Extension Directory](https://civicrm.org/extensions/):

    ```bash
    cv dl EXTENSION_1 EXTENSION_2 ...
    ```

    By default, it downloads stable releases. To download pre-releases (alphas/betas), use dev feed or an explicit URL:

    ```bash
    ## Download from the "dev" feed
    cv dl --dev EXTENSION_1 EXTENSION_2 ...

    ## Download from an explicit URL
    cv dl EXTESION_KEY@ZIP_URL
    ```

    Alternatively, if you downloaded an extension by other means, then you may enable it:

    ```bash
    cv en EXTENSION_1 EXTENSION_2 ...
    ```

??? info "Task: Manage settings"

    Many options are managed through the [Settings](https://docs.civicrm.org/dev/en/latest/framework/setting/) framework.
    These may be configured in the CLI:

    ```bash
    ## List current settings
    cv api setting.get

    ## Update specific settings
    cv api setting.create KEY_1=VALUE_1 KEY_2=VALUE_2 ...
    ```

??? info "Task: Clear caches"

    ```bash
    cv flush
    ```

<!--
??? example "Example: Install Mosaico and Shoreditch"

    Here's an example bringing the pieces together:

    ```
    cv dl --dev mosaico shoreditch
    cv api setting.create theme_backend=shoreditch
    ```
-->
