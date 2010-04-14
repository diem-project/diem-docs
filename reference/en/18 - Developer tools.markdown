Automated tests are great. Learn more about them on [symfony tests documentation](http://www.symfony-project.org/book/1_2/15-Unit-and-Functional-Testing).

## Functional tests

### Manual functional tests
You can write your own functional tests the same way you do with symfony.

>**IMPORTANT**
>When writing functional tests for Diem, you *must* replace the *sfBrowser* object with a *dmSfBrowser* one.
>Instead of
>~~~
>$browser = new sfTestFunctional(new sfBrowser());
>~~~
>You must write
>~~~
>$browser = new sfTestFunctional(new dmSfBrowser());
>~~~
>This is because in sfBrowser, the _sfContext_ class is hardcoded. But Diem has to override it with _dmContext_ to include the service container.

### Automatic functional tests
You can test all your front and admin pages without writing a line of code.

#### Admin functional test
    $ php symfony test:functional admin dm

The test configuration file can be found in _test/functional/admin/dmTest.php_

#### Front functional test
    $ php symfony test:functional front dm

The test configuration file can be found in _test/functional/front/dmTest.php_

#### Automatic functional test configuration

In the configuration file, we can see
[code php]
<?php
require_once realpath(dirname(__FILE__).'/../../../config/ProjectConfiguration.class.php');
require_once dm::getDir().'/dmCorePlugin/lib/test/dmAdminFunctionalCoverageTest.php';

$config = array(
  'login'     => true,    // whether to log a user or not at the beginning of the tests
  'username'  => 'admin', // username to log in
  'validate'  => false,   // perform html validation on each page, based on its doctype
  'debug'     => true,    // use debug mode ( slower, use more memory )
  'env'       => 'test',  // sf_environment when running tests
);

$test = new dmAdminFunctionalCoverageTest($config);

$test->run();
[/code]
Feel free to customize the test configuration for your needs.

Learn more about [symfony unit and functional testing](http://www.symfony-project.org/book/1_2/15-Unit-and-Functional-Testing).

## Unit tests
They just work like in symfony.

###Run Diem Core unit tests
The test will use an SQLite database and will never alter your project.
Just replace **/diem/path** with the path to Diem and run:
[code]
php /diem/path/prove
[/code]

##Browser detection
A getBrowser() method has been added to the user object. It returns an instance of the browser service, that describes the current user browser:
[code php]
$browser = $this->getUser()->getBrowser();

echo $browser->getName();        // Firefox
echo $browser->getVersion();     // 3.6
echo $browser->isUnknown();      // false
[/code]

The browser detection uses a very light and fast algorithm. It is not as efficient as heavy solutions like [browscap](http://code.google.com/p/phpbrowscap/). It will only detect browsers, and not bots.
If you want to change the way the broser is detected, you can [replace the browser service by your own](page:46).

##APC monitor
Use of APC is strongly recommended on both development and production servers. Diem will use it to store database and template cache.
A secured APC monitor is available in admin application. On the upper tool bar is a tiny bar indicating the APC load. By clicking on it you access to a complete APC monitor. From this monitor you are given informations about cache status, you can see opcode cache and user cache, and clear them globally or selectively.
Note : when refreshing the project, the whole APC cache is cleared.