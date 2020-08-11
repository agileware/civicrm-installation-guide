# Using TLS Encryption to connect to MySQL

CiviCRM can use TLS either with or without client certificates to connect to your MySQL database.

!!! warning "Initial Installation"
    At this time initial installation requires using the `cv core:install` method if you need TLS for MySQL during install. Alternatively you can install into a test database that does not require encryption and then move the database to the live server and update civicrm.settings.php.

    You must also manually update `CIVICRM_UF_DSN` in civicrm.settings.php after install if you are also using encryption for the CMS database.

## Client Certificates and Server Certificate Verification

### Do I need a client certificate?

The short answer is only if your host requires it. They will likely provide instructions on how to obtain one.

### Should I use one of the options that verifies the server certificate?

The short answer is yes for security reasons.

A longer answer is that the `host` parameter you set in the configuration must match exactly the name in the server certificate for verification to work. For example if the name in the server certificate is db435.examplehost.com, and you put the IP address or an alias in the `host` parameter, verification will fail even if the network connection succeeds. This is also likely to come up if you are testing in a local development environment where you often use "localhost" as the host.

## Installing with `cv core:install`

If you choose to use `cv core:install` to install the database over TLS, the only additional parameter you need to specify on the command line is the `--db` parameter for the CiviCRM database. The options are the same as under DSN Settings below.

After install if you are also using MySQL encryption for the CMS database you will need to update CIVICRM_UF_DSN in civicrm.settings.php.

## DSN Settings

In civicrm.settings.php for CIVICRM_DSN and/or CIVICRM_UF_DSN, you have two choices.

1. If using a client certificate, any or all of the following parameters can be added to the dsn string:

    | Parameter | Value | Comment |
    | --------- | ------------- | ------- |
    | key | /path/to/your.key | The client certificate private key. |
    | cert | /path/to/your.crt | The public client certificate. |
    | ca | /path/to/ca.crt | Certificate Authority. If using a self-signed certificate, see the note above about verification and hostname. |
    | capath | /path/to/folder/containing/ca/certs | |
    | cipher | a list of allowable ciphers | |

    The parameters should be urlencoded, e.g. spaces in paths are %20, forward slashes are %2F, etc. Do not use a `+` for spaces.

    | Example | Setting |
    | ------- | ------- |
    | Client certificate, and verify server certificate | `define('CIVICRM_DSN', 'mysqli://user:pass@hostname/dbname?new_link=true&key=%2Fpath%2Fto%2Ffile.key&cert=%2Fpath%2Fto%2Ffile.crt&ca=%2Fpath%2Fto%2Fca.crt');` |
    | No client certificate, but verify server certificate | `define('CIVICRM_DSN', 'mysqli://user:pass@hostname/dbname?new_link=true&ca=%2Fpath%2Fto%2Fca.crt');` |

2. If not using a client certificate and you don't want to verify the server certificate, use none of the parameters in (1) above and instead use ssl=1. This parameter is not in the mysqli documentation and is specific to CiviCRM. Example: `define('CIVICRM_DSN', 'mysqli://user:pass@hostname/dbname?new_link=true&ssl=1');`
