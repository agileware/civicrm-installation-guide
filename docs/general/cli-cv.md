!!! warning "Page rewrite in progress"

    This content was extracted from another page. It needs to be edited to work as a standalone page.

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
