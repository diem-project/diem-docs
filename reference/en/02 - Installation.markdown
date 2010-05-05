As a full stack CMF/CMS, Diem is designed to be used from the beginning of your project.
You should create the project with Diem, instead of plugging Diem into an existing project.

## Download Diem
Get the latest package on the [download page](page:6).
Diem embeds symfony, and will live outside of your project.

## Create the project

Let's assume that you have extracted Diem into **/path/to/diem**.

We can now create the project directory:

    mkdir myProject
    cd myProject

and launch the symfony command line installer. Don't forget to replace **/path/to/diem** by the correct value.

    php /path/to/diem/install

## Configure the project

### Server check
The installer will check that your server matches the [requirements](page:23).
Please note that the PHP CLI can use a different php.ini file than the one used with your web server.
You will be able to run the server check from your browser, after the installation is complete : http://mysite.com/dm_check.php

### Configuration questions
You will be given the possibility to configure the project through some simple questions.

#### Main language
The language is a two chars identifier like "en", "fr", "es".
If your project uses more than one language, enter the first, most important one. Defaults to "en".

#### Web directory name
Project web path, relative to the project root dir. This is the directory where the CSS and JS files live. Its content is public and accessible from Internet.
It is generally "web", "html" or "public_html".
If you don't know, choose "web". You will be able to change it later.

#### Database Management
You can choose between MySQL, PostgreSQL and SQLite.

#### Database name
The name of the database that will be created. By default, the project name will be used. Note that the database name can not contain a "-".

#### Database host
The hostname of the database.

#### Database user name
The database user to read and write the database. This needs to be an existing user, so if necessary use your database admin tools to create a new user.

#### Database password
The password of the database user specified in the previous step. **It will be used as the default website admin password**.

>**Mistakes?**
>If you make a mistake entering the database options the install script will loop back and give you a chance to enter the correct details

>**Database creation**
>If the database user you provided has sufficient permissions to create the database, Diem will do it for you.
>On the other case, you will have to create manually the empty database.

## Login to administration

When the installer complete, your project is ready for web access.

Two applications have been created:

- **front** is the public interface of your website
- **admin** is the secured backoffice

You can login right now to the admin application with the url
**http://www.myproject.com.localhost/admin_dev.php**

Your login is "admin" and your password is the database password you chose during installation.

> **Configure the web server**
> symfony provides help on how to [configure your web server](http://www.symfony-project.org/getting-started/1_3/en/05-Web-Server-Configuration)
> The "secure way" is highly recommended even in dev environments.

If you experience problems during installation, please report them to the [Diem google group](http://groups.google.com/group/diem-users)

## What's next ?

You may want to start the [tutorial](page:48) to build a first website.