# WP Dependency Installer Examples

Examples for use of wp-dependency-installer framework.

## Installation

Install the package via composer.

Run the composer command: ```composer require afragen/wp-dependency-installer```

To update, run the command ```composer update```

Then create a new `wp-dependencies.json` file.

```cp ./wp-dependencies-example.json ./example-plugin/wp-dependencies.json```

You will then need to update `wp-dependencies.json` to suit your requirements.

Add the following lines to your plugin or to your theme's `functions.php` file.

```php
include_once( __DIR__ . '/vendor/autoload.php' );
WP_Dependency_Installer::instance()->run( __DIR__ );
```

## JSON config file format

This file must be named `wp-dependencies.json` and it must be in the root directory of your plugin or theme.

You may use `required` or `optional` as interchangable opposites. If a plugin is required then it is not optional, and the reverse is true.

```json
[
  {
    "name": "Query Monitor",
    "host": "wordpress",
    "slug": "query-monitor/query-monitor.php",
    "uri": "https://wordpress.org/plugins/query-monitor/",
    "required": true
  },
  {
    "name": "GitHub Updater",
    "host": "github",
    "slug": "github-updater/github-updater.php",
    "uri": "afragen/github-updater",
    "branch": "master",
    "required": true,
    "token": null
  },
  {
    "name": "Test Plugin Notags",
    "host": "bitbucket",
    "slug": "test-plugin-notags/test-plugin-notags.php",
    "uri": "https://bitbucket.org/afragen/test-plugin-notags",
    "branch": "master",
    "optional": true
  },
  {
    "name": "Test Gitlab Plugin2",
    "host": "gitlab",
    "slug": "test-gitlab-plugin2/test-gitlab-plugin2.php",
    "uri": "https://gitlab.com/afragen/test-gitlab-plugin2",
    "branch": "develop",
    "optional": true,
    "token": null
  },
  {
    "name": "Test Direct Plugin Download",
    "host": "direct",
    "slug": "test-direct-plugin/test-plugin.php",
    "uri": "https://direct-download.com/path/to.zip",
    "required": false
  }
]
```

An example file is included, `wp-dependencies-example.json`. You may use a shorthand uri such as `<owner>/<repo>` in the JSON.

If you want to programmatically add dependencies you can send an associative array directly to

```php
WP_Dependency_Installer::instance()->register( $config, __DIR__ );
WP_Dependency_Installer::instance()->run( __DIR__ );
```

where `$config` is an associative array as in identical format as `json_decode( wp-dependencies.json content )`

The default timeout for dismissal of a notification is 7 days. There is a filter `wp_dependency_timeout` to adjust this on a per project basis.

```php
add_filter(
  'wp_dependency_timeout', function( $timeout, $source ) {
    $timeout = basename( __DIR__ ) !== $source ? $timeout : 14;
    return $timeout;
  }, 10, 2
);
```

To help the end user before installation, you can also display your plugin name within dismissable notifications through the `wp_dependency_dismiss_label` filter:

```php
add_filter(
  'wp_dependency_dismiss_label', function( $label, $source ) {
    $label = basename( __DIR__ ) !== $source ? $label : __( 'My Plugin Name', 'my-plugin-domain' );
    return $label;
  },
  10,
  2
);
```

The download link can be filtered using the filter hook `wp_dependency_download_link`. The `$download_link` and the `$dependency` are passed as parameters.
