# Using non-latin characters in PDF output

(This does not apply to Drupal 8/9.)

By default there are certain places in CiviCRM where PDF output does not handle non-latin characters because the unicode font files have been removed from the DOMPDF library. For example CiviCRM uses it when producing PDF copies of CiviReports, but by comparison it uses a different library for Mailing Labels.

Note when testing to see if you've succeeded, use letters that are entirely outside the latin alphabet, e.g. use cyrillic letters. Several letters like `ü` will work either way so aren't a good test.

??? example "Option A: Install the unicode font files for DOMPDF"

    You will need to repeat these steps after each CiviCRM upgrade.

    1. Get the dompdf download zip file from the github repository, e.g. at https://github.com/dompdf/dompdf/releases/tag/v0.8.6.
      * You might want to use the exact same release (github tag) that core uses. For the font it shouldn't matter, but you can find this number by looking in composer.lock in your civicrm root folder and searching for "dompdf" and look at the "version" field.
    1. Extract the files and look in the dompdf/lib/fonts folder.
    1. Copy all the DejaVuSans files over to your `<civicrm_root>/vendor/dompdf/dompdf/lib/fonts` folder.
    1. Edit the `dompdf_font_family_cache.dist.php` file in the same folder and add these lines:
    ```php
    'dejavu sans' =>
        [
            'bold' => DOMPDF_DIR . '/lib/fonts/DejaVuSans-Bold',
            'bold_italic' => DOMPDF_DIR . '/lib/fonts/DejaVuSans-BoldOblique',
            'italic' => DOMPDF_DIR . '/lib/fonts/DejaVuSans-Oblique',
            'normal' => DOMPDF_DIR . '/lib/fonts/DejaVuSans'
        ],
    'dejavu sans mono' =>
        [
            'bold' => DOMPDF_DIR . '/lib/fonts/DejaVuSansMono-Bold',
            'bold_italic' => DOMPDF_DIR . '/lib/fonts/DejaVuSansMono-BoldOblique',
            'italic' => DOMPDF_DIR . '/lib/fonts/DejaVuSansMono-Oblique',
            'normal' => DOMPDF_DIR . '/lib/fonts/DejaVuSansMono'
        ],
    'dejavu serif' =>
        [
            'bold' => DOMPDF_DIR . '/lib/fonts/DejaVuSerif-Bold',
            'bold_italic' => DOMPDF_DIR . '/lib/fonts/DejaVuSerif-BoldItalic',
            'italic' => DOMPDF_DIR . '/lib/fonts/DejaVuSerif-Italic',
            'normal' => DOMPDF_DIR . '/lib/fonts/DejaVuSerif'
        ],
    ```

??? example "Option B: Install wkhtmltopdf instead"

    This may also have an advantage for reports that cause DOMPDF to run out of memory. A possible disadvantage is the output may not be exactly the same or as well tested with CiviCRM as DOMPDF since it is not the default. You only need to do this once.

    1. Your operating system's package manager may already make it available to install. If not then go to https://wkhtmltopdf.org and download the file for your operating system.
    1. Note the full path to the executable binary.
    1. In CiviCRM go to Administer - System Settings - Misc.
    1. A couple lines down there is a setting `Path to wkhtmltopdf executable`.
    1. Fill it in, e.g. `/usr/local/bin/wkhtmltopdf`
    1. Click the Save button.
