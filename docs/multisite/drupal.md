# Drupal Multisite

## Install the first site

Say `site1.example.com` which would be same as any other normal CiviCRM installation.

It is not necessary to for the top level site to be multisite aware but if you want it to be then enable multisite by visiting `civicrm/admin/setting/preferences/multisite?reset=1`. If you do this you should also grant the associated permission in Drupal: "CiviCRM: administer Multiple Organizations" to your developers or website administrators.

Create a new contact to be the domain contact for the new domain (note steps 2 & 3 can now be done with the `MultisiteDomain.create` api which is part of the extension)

## Create new database records

You can create the database records in this section automatically if you prefer by installing the [Multisite Permissioning](https://civicrm.org/extensions/multisite-permissioning) extension and using the `MultisiteDomain.create` api as described in the [extension's README file](https://lab.civicrm.org/extensions/multisite/-/blob/master/README.md).

### Create the "Default Organization" record
Each CiviCRM domain must have its own [Default Organization](https://docs.civicrm.org/sysadmin/en/latest/setup/#site-configuration). Create a new Organization record to represent the default organization of the new domain.

### Create the domain record

Insert a new domain record into the CiviCRM database. For example if your new contact is 555

```sql
INSERT INTO `civicrm_domain` (
  `name`,
  `description`,
  `version`,
  contact_id)
SELECT
  'site 2',
  'second test site',
  cd.version,
  555
FROM civicrm_domain cd
WHERE cd.id = 1;
```

### Build the navigation links for new domain

Modify `civicrm_codebase/sql/civicrm_navigation.mysql` file and specify new domain, e.g

```sql
SELECT @domainID := id
FROM civicrm_domain
WHERE `name` = 'Default Domain Name';
```

To

```sql
SELECT @domainID := id
FROM civicrm_domain
WHERE `name` = 'site 2';
```

OR

```sql
SET @domainID = 2;
```

And import this file to your CiviCRM database.

## Create the new site

Setup the CMS for the second site.  In some cases, you will install a new instance of the CMS.  You can also use the existing CMS if you are using the [Domain Access](https://www.drupal.org/project/domain) module for Drupal.

### Locate the settings file for the second site

Depending on how you would like to install CiviCRM for site 2, proceed to one of the following steps:

### Manual CiviCRM installation for site2.example.com.

* Copy CiviCRM settings file from previous site (site1) to the new site (site2).

    ```
    cp sites/site1.example.com/civicrm.settings.php sites/site2.example.com/
    ```

    Note `sites/site2.example.com/civicrm.settings.php` is your new settings file which needs to be modified.

* The sites can share a single instance of the CiviCRM code in sites/all/modules/ directory if they are using the same CMS.

* Modify `sites/site2.example.com/civicrm.settings.php` file to adjust settings like `CIVICRM_TEMPLATE_COMPILEDIR`, `CIVICRM_UF_BASEURL` as per new site.
* Enable the CiviCRM module through Drupal, at Site building -> Modules ( `/admin/build/modules` ).

### Drupal with the Domain Access module

!!! note
    If you're using Drupal with Domain Access, consider installing the [Domain Access CiviCRM](https://civicrm.org/extensions/domain-access-civicrm) module if users might be creating new Drupal accounts via CiviCRM profiles.

Similar to WordPress, with Domain Access module there is only one copy of the codebase, and one copy of `civicrm.settings.php`.

The easiest is to assign the same domain_id's in Drupal and CiviCRM, then you can just replace this line:

```php
define( 'CIVICRM_DOMAIN_ID' , 1 );
```

With:

```php
if (!function_exists('domain_get_domain')) {
  // The Domain Access module is not enabled
  define('CIVICRM_DOMAIN_ID', 1);
}
else {
  // Get domain_id from the Drupal setup - Drupal and CiviCRM domain IDs MUST match!
  $domain = domain_get_domain();
  define('CIVICRM_DOMAIN_ID', $domain['domain_id']);
}
```

Otherwise you must replace the above line with a conditional similar to:

```php
//define the per-domain settings here first in case we're running from CLI (e.g. bin/csv/import.php)
if(empty($_SERVER['SERVER_NAME'])) {
  define( 'CIVICRM_DOMAIN_ID', 1 );
  define( 'CIVICRM_UF_BASEURL' , 'http://example.local/' );
}
```

```php
switch ($_SERVER['SERVER_NAME']) {
  case 'example.local':
    define( 'CIVICRM_DOMAIN_ID', 1 );
    define( 'CIVICRM_UF_BASEURL' , 'http://example.local/' );
    break;
  case 'cdp.example.local':
    define( 'CIVICRM_DOMAIN_ID', 2 );
    define( 'CIVICRM_UF_BASEURL' , 'http://cdp.example.local/' );
    break;
  case 'svp.example.local':
    define( 'CIVICRM_DOMAIN_ID', 3 );
    define( 'CIVICRM_UF_BASEURL' , 'http://svp.example.local/' );
    break;
}
```

With either of the above, skip the following "Register a new domain" step.

### Auto + manual installation for site2.example.com

* Use CiviCRM installer for installing CiviCRM for `site2.example.com`. Specify a new CiviCRM database which can be dropped later.
  Note `sites/site2.example.com/civicrm.settings.php` is your new settings file which needs to be modified.

* Adjust `CIVICRM_DSN` setting to use the CiviCRM database used by site1. Drop the new CiviCRM database previously created.

* The sites can share a single instance of the CiviCRM code in `sites/all/modules/` directory.

### Register a new domain

Modify located `civicrm.settings.php` file (for site2) to change following line -

```php
define( 'CIVICRM_DOMAIN_ID' , 1 );
```

to reflect the id of inserted domain record in step2. Assuming id is 2 for newly inserted record, the line would change to -

```php
define( 'CIVICRM_DOMAIN_ID' , 2 );
```

### Register a domain group

In multi-org installation, you can configure a group for each multisite aware domain (not necessarily including the top level domain). When you login to the site and go to "manage groups" screen, you will notice a group with the name as that of domain. System requires you to register this master group responsible for holding sub-groups/contacts. Contacts using the site will be automatically added to the site. You need to set a different group for each site at `civicrm/admin/setting/preferences/multisite?reset=1`

Note that it is valid not to enable multisite on the top level domain (which is unaware of the subdomains). This improves performance by not creating such a deep hierarchy of groups

### Associate an organization with the master group

To represent a multi-org hierarchy, an organization could be connected to the master group (one-to-one relationship). Add a record in the civicrm_group_organization table to represent this (or use the MultisiteDomain.create api to create your domain). This record only has value if the multisite extension is enabled.

### Configure Access Control

Here we restrict users on Site N to contacts in Site N's Site Group.

There is a separate extension called **multisite** that automatically implements CiviCRM access control for multisite installations, such that normal users on Site N are restricted to contacts in Site N's Site Group. Note that users with permission to view or edit all contacts bypass this access control.
