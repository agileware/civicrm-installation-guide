# WordPress Multisite

If using a WordPress multisite when CiviCRM is activated the `civicrm.settings.php` file is added within the [uploads directory for that site](https://wordpress.org/support/article/multisite-network-administration/#uploaded-file-path). Site 1, generally the main site of the multisite, uses the default upload path `/wp-content/uploads/civicrm`.

If you are looking to share one CiviCRM database across multiple websites, for a chapter like setup, the following needs to be added to your `wp-config.php` file.

``` php
// define CiviCRM constants here
if ( ! defined( 'CIVICRM_SETTINGS_PATH' ) ) {
        define( 'CIVICRM_SETTINGS_PATH', '/path/to/wordpress/website/wp-content/uploads/civicrm/civicrm.settings.php' );
}
```

This will define the path for all websites on the multisite and when enabled they will access the CiviCRM instance defined by the path settings and access is managed through the `civicrm.settings.php` as this example outlines.

## WordPress Multisite using Subdomains
If you have your multisite set up as subdomains, follow these steps.

!!! note "Change your `civicrm.settings.php` file"
Keep in mind, you should **only** change the `civicrm.settings.php` file that lives in `wp-content/uploads/civicrm` (the parent/main site's uploads folder).

Find the block of code that resembles this:
    
```php
if (!defined('CIVICRM_UF_BASEURL')) {
  define( 'CIVICRM_UF_BASEURL'      , 'http://example.org/');
}
```

**Remove it entirely**, and replace it with the following code:

```php
function civicrm_multisite_get_domain_data() {
    // Define CiviCRM Multisite Domain data.
    $data = array(
        'https://example.org' => array(
	    'domain_id' => 1,
	    'domain_group_id' => 7,
	    'domain_org_id' => 1,
	    //'wp.frontend.base.url' => 'https://example.org/',
	    //'wp.backend.base.url' => 'https://example.org/wp-admin/',
	    'extensionsDir' => '<path to extensions folder>',
	    'extensionsURL' => '<URL to extensions directory>',
	    'userFrameworkResourceURL' => 'https://example.org/wp-content/plugins/civicrm/civicrm/',
        ),
	'https://sub.example.org' => array(
	    'domain_id' => 21,
	    'domain_group_id' => 13,
	    'domain_org_id' => 35603,
	    //'wp.frontend.base.url' => 'https://sub.example.org/',
	    //'wp.backend.base.url' => 'https://sub.example.org/wp-admin/',
	    'extensionsDir' => '<path to extensions folder>',
	    'extensionsURL' => '<URL to extensions directory>',
	    'userFrameworkResourceURL' => 'https://example.org/wp-content/plugins/civicrm/civicrm/',
	),
	'https://sub0.example.org' => array(
	    'domain_id' => 20,
	    'domain_group_id' => 23,
	    'domain_org_id' => 35604,
	    //'wp.frontend.base.url' => 'https://sub0.example.org/',
	    //'wp.backend.base.url' => 'https://sub0.example.org/wp-admin/',
	    'extensionsDir' => '<path to extensions folder>',
	    'extensionsURL' => '<URL to extensions directory>',
	    'userFrameworkResourceURL' => 'https://example.org/wp-content/plugins/civicrm/civicrm/',
        ), 
    )
}
```   

Rinse and repeat for any additional subdomains.

Big thanks to **Christian Wach** for [this code](https://gist.github.com/christianwach/5ca120670152df3dbfb8d3ca42079a96), which was adapted to fit subdomain setup.

You do **not** need to do the **Create the domain record** step (stated above) if you use the [multisite extension](https://lab.civicrm.org/extensions/multisite). It is recommended you copy and paste the following command into your shell/terminal:

`wp cv api MultisiteDomain.create debug=1 sequential=1 name="Sub1ExampleOrg" is_transactional=0 group_id="2" contact_id="4"`

!!! note "Important Note"

    * `Sub1ExampleOrg`: This should be replaced with a name for the domain.
    * `group_id`: This can be found as the `group_id` column in the table called `civicrm_group_organization`. 
    * `contact_id`: This can be found as the `organization_id` column in the table called `civicrm_group_organization`.

**You will need to do this for each subdomain, so keyboard shortcuts are strongly encouraged.**

After you've done this, if you don't have it already, download and install the [CiviCRM Admin Utilities plugin for WordPress](https://wordpress.org/plugins/civicrm-admin-utilities/). **Make sure it (and CiviCRM itself) is Network Activated!** You will need it in a second.

Visit one of your sites via it's subdomain in your browser. 

Along your left menu, find **Settings**, and hover over it. Click **CiviCRM Admin Utilities** when the menu pops out.

Scroll to the bottom, find where it says **Miscellaneous Utilities**.

1. Check the box next to **Clear Caches**.
2. Once it refreshes the page, return to the CiviCRM Admin Utilities page. Click the link that says **Click this to rebuild the CiviCRM menu.**
3. Again, once it refreshes, return to the CiviCRM Admin Utilities page. Click the last link that says **Click this to rebuild the triggers in the CiviCRM database.**

Once you have that, you should be good to go!

**However...**

If you still experience errors or problems at this point, make sure to clear your browser cache. If to no avail, try private/incognito browsing. Finally, as a last point, clear your DNS cache.

* Windows (Terminal/CMD): `ipconfig /flushdns`
* Mac/Linux: `sudo killall -HUP mDNSResponder`

**Recommendation**: Create a Site Admin role for any administrator of sub-sites that are sharing CiviCRM, meaning the default Administrator role is only used on the main site. This is recommended since by default all Adminstrators automatically get access to Administer CiviCRM and will be able to create things like custom data fields. By creating a Site Admin role the CiviCRM permissions can be managed from [Administer > Users and Permissions > Permissions (Access Control)](https://docs.civicrm.org/user/en/latest/initial-set-up/permissions-and-access-control/#access-control-permissions-in-wordpress) and will allow only main site administrator access to Administer CiviCRM.
