Diem 5.0 is based on [symfony 1.4](http://symfony-project.org) and [Zend Framework 1.9](http://framework.zend.com).
It will only run on a Unix server with apache2 & PHP >= 5.2.4 or php 5.3.x

## Required

Configuration | Required | Info
-|-|-
Server | **Unix** | Windows server is not tested yet and will probably not work
PHP version | **>= 5.2.4** | PHP 5.3 is now tested, and it works
PHP C.L.I. | **ON** | A php command line interface is required during installation and maintenance of the project
memory_limit | **>= 48Mo** | Diem front pages require less than 16Mb. But sometime, when an administrator performs modifications, Diem needs more memory. Page synchronization, search index update and image resizing may require 48Mo
pdo_mysql or pdo_pgsql or pdo_sqlite | **ON** | Diem supports MySQL, PostGreSQL and SQLite
json | **>= 1.0** | json_encode is used to communicate with JavaScript
gd | **ON** | GD is heavily used to resize front images and provide admin graphs

## Recommended

Configuration | Recommended | Info
-|-|-
APC | **ON** | Diem will use APC if present to store all kind of cache, and dramatically improve performances. If APC is not available, file cache will be used instead
graphviz | **ON** | if graphviz is installed on your server, Diem will be able to generate dependence and database diagrams
upload_max_filesize | **>= 2Mo**
mbstring | **ON**
magic quote gpc | **OFF**
register_globals | **OFF**
session.auto_start | **OFF**
pcre | **ON** | Used by Zend_Search_Lucene
token_get_all | **ON**
A modern browser | Firefox >= 3, Safari 4, Opera >= 9, Google Chrome | Administrator interface is not yet tested with poor browsers like IE, and will probably not work
PHP != 5.2.9 | | PHP 5.2.9 broke array_unique() and sfToolkit::arrayDeepMerge(). Use 5.2.10 instead [Ticket #6211]

## See also

- The [symfony prerequisites page](http://www.symfony-project.org/getting-started/1_4/en/02-Prerequisites)
- The [Zend Framework requirements page](http://framework.zend.com/manual/en/requirements.html)


test