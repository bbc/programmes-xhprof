README
======

[![Build Status](https://travis-ci.org/jonaswouters/XhprofBundle.svg?branch=master)](https://travis-ci.org/jonaswouters/XhprofBundle) [![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/jonaswouters/XhprofBundle/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/jonaswouters/XhprofBundle/?branch=master) [![Code Coverage](https://scrutinizer-ci.com/g/jonaswouters/XhprofBundle/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/jonaswouters/XhprofBundle/?branch=master)

What is XHProf?
---------------

XHProf is a hierarchical profiler for PHP. It reports function-level call counts and inclusive and exclusive metrics such as wall (elapsed) time, CPU time and memory usage.
A function's profile can be broken down by callers or callees. The raw data collection component is implemented in C as a PHP Zend extension called xhprof.
XHProf has a simple HTML based user interface (written in PHP). The browser based UI for viewing profiler results makes it easy to view results or to share results with peers.
A callgraph image view is also supported.

See [official documentation][1]

What does this Symfony 2 Bundle do?
-----------------------------------

This bundle helps you to easily use the XHProf bundle with the web debug toolbar in Symfony 2.
With Symfony 2.3 and newer, it can also profile console commands. It comes with [github://preinheimer/xhprof][3] bundled,
you just have to create a link in your project's web folder to `vendor/bbc/programmes-xhprof/preiheimer/xhprof`.

## Installation

For a few examples of how to setup this bundle, check 
[https://github.com/bbc/programmes-frontend/tree/profiling-build](github://bbc/programmes-frontend/profiling-build)
or [https://github.com/bbc/programmes-clifton/tree/profiling-build](github://bbc/programmes-clifton/profiling-build). 
The compare view is quite useful to see the setup bits.

Make sure you have XHProf installed.
If you are on a mac you can easily install it via [Macports][2]
    sudo port install php5-xhprof

If you are using PHP 7 you can use the [Tideways XHProf fork](https://tideways.io/profiler/xhprof-for-php7-php5.6).

1. ### Composer

Add the following dependencies to your projects composer.json file:

```json
"require": {
    "jns/xhprof-bundle": "1.0.*@dev",
    "lox/xhprof": "dev-master@dev"
}
```

Of course, you have to install [xhprof library](http://php.net/manual/fr/book.xhprof.php) (or for PHP 7 the
the [Tideways XHProf fork](https://tideways.io/profiler/xhprof-for-php7-php5.6)) in your server.
At this moment, `ext-xhprof` is not required because your application could be deployed to a server without xhprof.

2. ### Old way by adding to your vendor/bundles/ dir

1. #### To install the bundle, place it in the `src/Jns/Bundle` directory of your project
(so that it lives at `src/Jns/Bundle/XhprofBundle`). You can do this by adding
the bundle as a submodule, cloning it, or simply downloading the source.

```bash
$ git submodule add https://github.com/jonaswouters/XhprofBundle.git src/Jns/Bundle/XhprofBundle
```

2. #### Add the Jns namespace to your autoloader

If this is the first Jns bundle in your Symfony 2 project, you'll
need to add the `Jns` namespace to your autoloader. This file is usually located at `app/autoload.php`.

```php
$loader->registerNamespaces(array(
    'Jns' => __DIR__.'/../src'
    // ...
));
```

### Initializing the bundle

To initialize the bundle, you'll need to add it in your kernel. This
file is usually located at `app/AppKernel.php`. Loading it only in your dev environment is recommended.

```php
public function registerBundles()
{
    // ...

    if (in_array($this->getEnvironment(), array('dev', 'test'))) {
        // ...
        $bundles[] = new Jns\Bundle\XhprofBundle\JnsXhprofBundle();
    }
}
```

## Configuration

### Summary
| Setting                  | Explanation                                                                                                      | Default                                     |
|--------------------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------|
| location_web             | Link to xhprof GUI, used in the debug bar                                                                        | http://xhprof                               |
| manager_registry         | Doctrine's registry manager                                                                                      | doctrine                                    |
| entity_manager           | Entity manager that has the Detail entity                                                                        | default                                     |
| entity_class             | Detail entity, store's the profile data in the database                                                          | Jns\Bundle\XhprofBundle\Entity\XhprofDetail |
| enable_xhgui             | Enable Xhgui. Without this, nothing gets stored in the database                                                  | False                                       |
| exclude_patterns         | URL patterns to be excluded during profiling                                                                     |                                             |
| sample_size              | Probability of profiling. The larger the number, the smaller the chance                                          | 1                                           |
| enabled                  | Enable or disable profiling                                                                                      | False                                       |
| require_extension_exists | Require the extension exists to run                                                                              | True                                        | 
| skip_builtin_functions   | Don't profile PHP built-in functions                                                                             | False                                       |
| request_query_argument   | Only activate on requests that have this argument                                                                | False                                       |
| response_header          | Custom Response header                                                                                           | X-Xhprof-Url                                |
| server_id                | ID of the server you are profiling                                                                               | default                                     |
| command                  | on: Always profile, off: Never profile, option: only profile if option specified in command_option_name is given | option                                      |
| command_option_name      | If "command" is set to "option", this is the name of the additional option that all commands get                 | xhprof                                      |
| command_exclude_patterns | List of regular expressions to match commands that are not profiled                                              |                                             |


### Configure the XHProf locations.

The Bundle comes preconfigured for the macports php5-xhprof default installation,
with the xhprof web located at http://xhprof.localhost.
To change these settings for your environment you can override the defaults by
defining the following settings in your config. The config is usually located at `app/config/config_dev.yml`.

```yaml
jns_xhprof:
    location_web:    http://xhprof.localhost
    enabled:         true
```

Do not forget to set `enabled` to `true`, or the profiler will never be activated.

### Using XHGui

[XHGui][3] is a GUI for the XHProf PHP extension, using a database backend, and pretty graphs to make it easy to use and interpret. The XHProf bundle supports using XHGui to display the results. To use, install XHGui, and add the following two settings to the configuration, usually located at `app/config/config.yml`:

```yaml
jns_xhprof:
    entity_manager:  <name_of_entity_manager> (defaults to default)
    entity_class:    Acme\FooBundle\Entity\XhprofDetail
    enable_xhgui:    true
```

Create your class `Acme\FooBundle\Entity\XhprofDetail`:

```php
<?php

namespace Acme\FooBundle\Entity;

use Jns\Bundle\XhprofBundle\Entity\XhprofDetail as BaseXhprofDetail;
use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity
 * @ORM\Table(name="details")
 */
class XhprofDetail extends BaseXhprofDetail
{
    /**
     * @var integer $id
     *
     * @ORM\Column(name="id", type="string", unique=true, length=17, nullable=false)
     * @ORM\Id
     */
    protected $id;
}
```

If you only have one entity manager defined, you don't need to set it here. This setting is for the case where you are using a seperate profiling database for XHGui (highly recommended).

### Specifying a Sample Size

You can specify a sample size for profiling. This is useful to collect random
samples of real requests in production environments. If you have plenty of
requests, you really don't want to profile all of them.

The sample size is set as a probability for profiling, so for example, if you
set the sample size to 2, then on average, every second request will be profiled.
Of course, in production you want to set it to a much higher value. Defaults
to 1, so that every request will be profiled.

```yaml
jns_xhprof:
    sample_size: 2
```

### Disable built-in functions

You can skip all built-in (internal) functions.

```yaml
jns_xhprof:
    skip_builtin_functions: true
```

### Web request profiling

#### Enabling XHProf only for requests with a trigger parameter

You can specify a `request_query_argument` parameter to have XHProf only activate
on requests that have this argument. This can be useful to profile a production
system without impacting other requests too much.

```yaml
jns_xhprof:
    request_query_argument: "__xhprof"
```

#### Enabling XHProf only for matching pattern request

It's possible to configure `exclude_patterns` parameter in configuration. XHProf would be enabled only for requests which will match these patterns.

```yaml
jns_xhprof:
    exclude_patterns: ['/css/', '/js/']
```

#### Using XHProf with disabled Symfony Profiler

The most common case is the `prod` mode. Symfony Profiler is disabled by default in this mode.
It is possible to configure XHProf Bundle to send custom Response header with XHProf web UI URL for the current token.
Header name could be configured with `response_header` parameter in bundle configuration.
Empty value disables this header completely. Default header name is `X-Xhprof-Url`.

### Console command profiling

#### Enabling console command profiling

You can set the profiling of console commands to `on`, `off` or `option`.

* `on`: all commands are profiled according to `sample_size`;
* `off`: no commands are profiled (but web requests might be profiled);
* `option`: commands get an additional option to trigger profiling.

```yaml
jns_xhprof:
    command: "off"
```

#### Enabling XHProf with a specific option only

When you set `command` to `option`, you can specify an option name that will
trigger a profiler run on a command. That option will automatically be available
on all commands.

```yaml
jns_xhprof:
    command: "option"
    command_option_name: xhprof
```

Now you can profile a command with

```bash
$ app/console acme:my:command --xhprof
```

#### Excluding some commands from profiling

When using the `on` setting, you might want to filter what commands get profiled.
If the name filter matches, the profiler will never trigger.

```yaml
jns_xhprof:
    command_exclude_patterns: ['acme:', ':debug']
```

[1]: http://php.net/manual/book.xhprof.php
[2]: http://www.macports.org/
[3]: https://github.com/preinheimer/xhprof
