Boomstone, a spark for PHP project.
===================================

Features
--------

* A file structure strongly inspired by [KnpLabs](http://knplabs.fr/)
* [MongoDB](http://www.mongodb.org/) document storage.
* [Boomgo](https://github.com/Retentio/Boomgo) tiny Object Document Mapper.
* __Console & Command__ aware of your Silex Application.
* __i18n__ command to extract strings from twig.
* Basic __user actions__: signup, signin & recovery password.
* Basic __security filter__.

Installation
------------

1. Vendors installations

```shell
# chown u+x bin/vendors
bin/vendors install
```

2. Create the cache dir

```
mkdir cache cache/boomgo
chmod -R 777 cache src/Resources/locales
```

3. Export twitter bootstrap assets

```
mkdir web/js/bootstrap
cp vendor/bootstrap/js/*.js web/js/bootstrap/
cp -R vendor/bootstrap/img web/
```

4. Configuration

```
cp app/config.php.dist app/config.php
```

How to use it
-------------

### Structure
The application boot with `app/bootstrap.php`.
This process requires a non-versioned `app/config.php`, to easily switch between environments (dev, test, prod...):

```php
<?php
require_once __DIR__.'/config.php';
// require_once __DIR__.'/config_dev.php';
// require_once __DIR__.'/config_test.php'
?>
```

Almost all code logic lives under the `src/` path.

### Console

Boomstone provides console "à la Symfony" : `php app/console`. The application aware command allows you to access your silex application.

```php
<?php
use Boomstone\Command\ApplicationAwareCommand;
class MyCommand extends ApplicationAwareCommand
{
    protected function execute(InputInterface $input, OutputInterface $output)
    {
        $this->getApplication() // return the console application
        $this->getApplication()->getSilexApplication(); // return your silex $app
        $app['twig'] // return your twig instance for example.
    }
}
?>
```
### I18n

1. Set a directory for your locales in the `app/config.php`.
2. Create sub directories for each locale used in your app: `locale_path/fr, locale_path/en`.
3. Ensure this directories are writable for your PHP process.
4. Use the command.

```
php app/console i18n:update [locale]
php app/console i18n:update fr
php app/console i18n:update en
```

You do not need to worry about your previous translated strings. They will be always preserved.
Yet this bring a limitation, actually old and unused strings are not removed.

You can use translation domains

```twig
{% trans from "homepage" %}Welcome dude !{% endtrans %}
{% trans from "error" %}Sorry, it's terrible.{% endtrans %}
```

This will output one file per domain

```
locale_path/fr/homepage.fr.xlf
locale_path/fr/error.fr.xlf
```

### Basic Security filter

It's just a simple example, no Interface provided. See the `before()` method in `app/boomstone.php`.

```php
<?php
  $app->get('/', function () use ($app) {
            // some logic
        })->value('security', array('ROLE_MEMBER'));
?>
```

### Styles
The _main_ stylesheet is `web/less/boomstone.less`, it import all the goodness from twitter bootstrap and is dedicated to your own rules. On MacOS, I recommend to use [LESS.app](http://incident57.com/less/): _a dev tool which watch a less directory and recompile them on a file change_.

Roadmap
-------

* Improve I18n command.
* Relax and get some PR from the open source community.

Limitations
-----------

* Translator class used is a modified copy from Symfony\Component\Translation (because of scope constraint)
* The i18n command was tested only with Xliff format
* The i18n command won't remove old and unused string (yet it appear to put them at the end of the file)
* The i18n command won't extract string from controller and forms (should be manually writen)

