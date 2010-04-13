If you don't find the solution here, try getting help on [google group](http://groups.google.com/group/diem-users), or #diem IRC channel on freenode.

>**How to solve 50% of the problems**
>Many times, when something goes wrong, running the command
>~~~
>$ php symfony dm:setup
>~~~
>will solve it, or at least help to diagnostic the problem.

## Allowed memory size of 8388608 bytes exhausted
Diem front pages require less than 16Mb. But sometime, when an administrator performs modifications, Diem needs more memory. Page synchronization, search index repopulation and image resizing may require 48Mo.

*Solution*: Edit the setting memory_limit in the php.ini file. The more, the better.

## Front modules / templates are not generated
Folder permissions are probably too restrictive, and Diem is not allowed to generate the files.

*Solution*: run the following command :

    $ php symfony dm:permissions

If some tests fail, change matching folders permissions with

    $ chmod -R 777 my_folder

Then, if the front, click the "Update project" button.
If front templates are still missing, run

    $ php symfony dmFront:generate

and you should see a diagnostic of the problem.

## The doctrine migration fails
Diem does not interfere with doctrine migrations.
_Solution:_ See the [symfony documentation](http://www.symfony-project.org/doctrine/1_2/en/07-Migrations) and the [doctrine documentation](http://www.doctrine-project.org/documentation/manual/1_2/en/migrations) about migrations.

## Models which translation is versionned don't work with PostGreSQL
This a known Doctrine bug. See: [http://www.doctrine-project.org/jira/browse/DC-135](http://www.doctrine-project.org/jira/browse/DC-135).

## It works badly with other browsers than Firefox
Yes indeed. Improve browser support is one of the priorities.
_Solution:_ use Firefox

## Admin diagrams are not generated
Diem needs **graphviz** installed on the server to generate the dependency injection and database diagrams.
###Some users reported that **dot** fails on Mac OSX.
Append in the file /Applications/MAMP/Library/bin/envvars this:

    export PATH="$PATH:/usr/local/bin"

and restart apache (MAMP)

## Stylesheets and JavaScripts are not loaded
Diem tells apache to send compressed assets in Gzip format to browsers that support it. Sometimes, depending on apache configuration, it may fail.

*Solution:* open *.htaccess* in your web directory, and remove or comment the lines between
\# SEND GZIPPED CONTENT TO COMPATIBLE BROWSERS
and
\# END GZIPPED CONTENT

## Why does not Diem xxxxxxxx ?
You can decide what Diem will become. We will focus developments on [what you need](http://diem.uservoice.com).