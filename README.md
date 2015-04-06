bash-onedrive-upload
====================

Upload files to [Microsoft OneDrive](https://onedrive.live.com) via linux command line using the [REST API](http://onedrive.github.io/).

Prerequisites
-------------

This script uses `curl` for accessing the API, and `grep` and `cut` for value extraction.

Getting started
---------------

Before you can use this tool, create an application in the [Microsoft account Developer Center](https://account.live.com/developers/applications) to generate your custom Client ID and Client secret. Notice, that you need to switch the API Setting "Mobile or desktop client app" to "yes".

Afterwards your overview should show your freshly created credentials in the form

    Client ID: 00000000A2B3C495
    Client secret: 88qC3kX2Cbd0tXV2sBnqYbS321abcDEF

Please insert these values in the matching variables in `onedrive.cfg`:

    export api_client_id="00000000A2B3C495"
    export api_client_secret="88qC3kX2Cbd0tXV2sBnqYbS321abcDEF"

After the initial configuration you must authorize the app to use your OneDrive account. Run

    $ ./onedrive-authorize

and follow the steps. You will need a web browser.

After the authorization process has successfully completed you can upload files (up to 100MB each due to API limitations for simple uploads).

Usage
-----

To upload a single file simply type

    $ ./onedrive-upload file1

You can also upload multiple files, either by explicitly specifying each one

    $ ./onedrive-upload file1 file2

or just use wildcards

    $ ./onedrive-upload file*.png

You can also specify a destination folder relative to the root folder configured in `onedrive.cfg`:

    $ ./onedrive-upload -f "relative/path" file1

This command will automatically determine all of the needed folder ids and recursively create all subfolders that do not yet exist.

Configuration
-------------

### Specify an alternate upload folder

If you want to use a folder other than the root folder of your OneDrive as your upload root folder, you need to retrieve its unique id.

Open your the [OneDrive Web Interface](https://onedrive.live.com) and navigate to the folder, you want to configure as your new upload folder. The address bar should now show a URL like

    https://onedrive.live.com/?cid=123419ACDA5678AB&id=123419ACDA5678AB%2145321

The GET-parameter `id` is the unique folder id you need to place in your `onedrive.cfg`. You have to work with the decoded URL, e.g. `%21` decodes to `!`:

    export api_folder_id="123419ACDA5678AB!45321"

Keep in mind that this will be the new root folder of your OneDrive as seen by this script. The script does *not* support escaping the root by using `../`.

### Number of simultaneous uploads

If the script does not fully utilize your bandwidth, you can maybe speed things up a little bit by increasing the value of `max_upload_threads`.

When you start the upload of more than one file, the script will start up to `max_upload_threads` parallel uploads, but only one thread per file.
