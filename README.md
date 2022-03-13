# Single-script solution for automated setup of a new Google Cloud Platform (GCP) project

The solution represents [a single script file](core/main.py) written in [Python 3](https://www.python.org/downloads/) and ensures that the following steps are performed:

* New [Google Cloud Platform](https://cloud.google.com/) project (GCP) is created
* [Firebase](https://firebase.google.com/) functionality is added to the previously created GCP project
* New Android application is added to the previously created Firebase project
* (optional) New iOS application is added to the previously created Firebase project
* Configuration artifact associated with the previously created Android application gets downloaded locally
* (optional) Configuration artifact associated with the previously created iOS application gets downloaded locally

Referenced APIs:

* [Cloud Resource Manager REST API](https://cloud.google.com/resource-manager/reference/rest)
* [Firebase Management REST API](https://firebase.google.com/docs/projects/api/reference/rest)

## Prerequisites

Before running the _main.py_ script, please make sure that you have successfully performed the following steps:

* Create a Google Cloud Platform account
* [Configure OAuth consent screen](https://developers.google.com/workspace/guides/configure-oauth-consent)
* [Create OAuth 2.0 Client ID and download the credentials JSON file to your local workspace](https://developers.google.com/workspace/guides/create-credentials#oauth-client-id)

**NOTE**: Your user (i.e. Gmail address) should be added as a test user on the OAuth consent screen

Downloaded credentials JSON file should have the following content (sensitive data replaced with mocks):

```
{
    "installed":{
        "client_id":"<CLIENT_ID>",
        "project_id":"<PROJECT_ID>",
        "auth_uri":"<AUTH_URI>",
        "token_uri":"<TOKEN_URI>",
        "auth_provider_x509_cert_url":"<AUTH_PROVIDER_URL>",
        "client_secret":"<CLIENT_SECRET>",
        "redirect_uris":[
            "<URI_1>",
            "<URI_2>"
        ]
    }
}
```

## First steps

* Clone this project to your local workspace - `git clone git@github.com:vunicjovan/gcp-project-automation-single-script.git`
* Open the cloned project with you favorite IDE (e.g. _PyCharm_)
* Install required dependencies - `pip install -r requirements.txt` (optional: use a separate virtual environment)
* Change the working directory to `core` with `cd core/`

## Running the script

The _main.py_ script should be run through use of command line arguments, referring to 2 specific cases:

1. All configuration data is entered through command line arguments
2. Only a single command line argument reffering to an external JSON configuration file is entered

**NOTE**: When running the script for the first time, you will be prompted to sign in with your Gmail account in a web browser

* This is required for the access/refresh token data to be obtained from _GCP_ API and stored into local `token.json` file.
* Each next run will perform the authentication and authorization automatically by reading from the `token.json` file.

### Available command line arguments

To see the available arguments, their definitions and usage hints, please run `python main.py -h` or `python main.py --help`.

This results in the following output:

```
usage: main.py [-h] [--auth AUTH] [--gcp_project_id GCP_PROJECT_ID] [--gcp_project_name GCP_PROJECT_NAME]
               [--android_app_name ANDROID_APP_NAME] [--android_package ANDROID_PACKAGE]
               [--ios_bundle_id IOS_BUNDLE_ID] [--ios_app_name IOS_APP_NAME] [--app_store_id APP_STORE_ID]
               [--android_config_path ANDROID_CONFIG_PATH] [--ios_config_path IOS_CONFIG_PATH]
               [--config_file CONFIG_FILE]

optional arguments:
  -h, --help            show this help message and exit
  --auth AUTH           Path to the credentials.json file obtained from Google Cloud Platform. Required if not     
                        using an external config file.
  --gcp_project_id GCP_PROJECT_ID
                        The unique, user-assigned id of the project. It must be 6 to 30 lowercase ASCII letters,   
                        digits, or hyphens. It must start with a letter. Trailing hyphens are prohibited.
                        Required if not using an external config file. Example: my-test-project-123
  --gcp_project_name GCP_PROJECT_NAME
                        A user-assigned display name of the project. When present it must be between 4 to 30       
                        characters. Allowed characters are: lowercase and uppercase letters, numbers, hyphen,      
                        single-quote, double-quote, space, and exclamation point. Example: My Test Project
  --android_app_name ANDROID_APP_NAME
                        The user-assigned display name for the AndroidApp. Example: My Test App
  --android_package ANDROID_PACKAGE
                        The canonical package name of the Android app as would appear in the Google Play
                        Developer Console. Required if not using an external config file. Example:
                        com.test.app.project
  --ios_bundle_id IOS_BUNDLE_ID
                        The canonical bundle ID of the iOS app as it would appear in the iOS AppStore. Must be     
                        specified when one of the following arguments is used: ios_app_name, app_store_id or       
                        ios_config_path.Example: com.test.app.project
  --ios_app_name IOS_APP_NAME
                        The user-assigned display name for the IosApp. Example: My Test App
  --app_store_id APP_STORE_ID
                        The automatically generated Apple ID assigned to the iOS app by Apple in the iOS App       
                        Store. Example: 123456789
  --android_config_path ANDROID_CONFIG_PATH
                        Desired path for the configuration artifact associated with the previously created
                        AndroidApp. Defaults to the current working directory. Example: C:/
  --ios_config_path IOS_CONFIG_PATH
                        Desired path for the configuration artifact associated with the previously created
                        IosApp. Defaults to the current working directory. Example: C:/
  --config_file CONFIG_FILE
                        Path to an external configuration file represented in JSON format. This argument is self-  
                        sufficient and if it is specified, all the other arguments will be automatically omitted. 
```

### Command line

An example call of the script with all the configuration data entered as command line arguments is shown below:

```
python main.py --auth path/to/your/credentials.json --gcp_project_id my-test-project-100 --gcp_project_name "My Test Project 100" --android_app_name "My Test App 100" --android_package com.my.android.ap100p --ios_bundle_id com.my.ios.ap100p --ios_app_name "My Test App 100" --app_store_id 123456789 --android_config_path D:/ --ios_config_path D:/
```

### External configuration file

An example call of the script with a single command line argument referring to an external JSON configuration file:

```
python main.py --config_file path/to/your/configuration.json
```

Example of a JSON configuration file:

```
{
    "auth":"path/to/your/credentials.json",
    "gcp_project_id":"my-test-project-100",
    "gcp_project_name":"My Test Project 100",
    "android_app_name":"My Android App 100",
    "android_package":"com.my.android.ap100p",
    "ios_bundle_id":"com.my.ios.ap100p",
    "ios_app_name":"My iOS App 100",
    "app_store_id":"123456789",
    "android_config_path":"D:/",
    "ios_config_path":"D:/"
}
```

## Notes on further development

As this solution represents a _Proof of Concept_ (PoC), there is a space for further development in various sections of the solution.

The following upgrades could be considered:

* Regular expression (RegEx) checks for the command line arguments (where needed)
* JSON schema check when using an external JSON configuration file
* Regular expression (RegEx) checks for parameter within API call methods (where needed)
* Current [GCPClient](https://github.com/vunicjovan/gcp-project-automation-single-script/blob/291b50c3278f818293abeb2a7b29f14a62150d74/core/main.py#L161) class could be divided to separate clients for _GCP_ and _Firebase_
* This solution could be used as a starting point for a whole client library, which would deal with _GCP_ and _Firebase_ APIs and could be installed through some of the existing package managing tools (e.g. _pip_)
