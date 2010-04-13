This chapter will only cover the Diem additions to symfony configuration system.

See the [symfony configuration documentation](http://www.symfony-project.org/book/1_2/05-Configuring-Symfony).

## Graphical configuration panel
Sometimes, a non-developer may need to modify some configuration.
We will not ask him to edit a .yml file...

### Use the configuration panel

A user with "config_panel" permission can access to the configuration panel:

![configuration panel](/uploads/screen/config_panel.png)

As all options are quite intuitive, and commented on the interface, we will not describe them here.

In the code, configuration is accessed by calling dmConfig::get()

[code php]
// get the site_name value
dmConfig::get('site_name')
// get the image_resize_quality value, or 95 if not set
dmConfig::get('image_resize_quality', 95)
[/code]

### Customize the configuration panel
It's possible to add new options into the configuration panel. This is reserved to the superadmin.
In the admin top menu, go to System -> CONFIGURATION -> Settings
You will see a list of the options shown in the configuration panel.
When adding or modifying a configuration option, the fields are :

- **name** the name used to fetch the value with dmConfig::get()
- **active** whether the option appears on the configuration panel or not
- **group** the tab where the option will be showed
- **credentials** the credentials required to modify the value, separated by a space.
- **type** the input type for this option
- **params** if **type** is a select, the select options. Example for the image_resize_method option : "fit=Fit scale=Scale inflate=Inflate top=Top right=Right left=Left bottom=Bottom center=Center"
- **value** the current option value
- **default** the default value when creating an option

## Configuration files

All configuration files support cascade (excepted for schema.yml).
It means that if you want a value to apply to the entire project, admin and front, you will modify it in the /config directory.
But if you want the value to only apply on front, you will modify it on the /apps/front/config directory.

### config / doctrine / schema.yml

This file is used to describe the data model.

The Diem schema.yml is a flavored version of the Doctrine one.

See how to [define models with Doctrine](http://www.doctrine-project.org/documentation/manual/1_2/en/defining-models).

Diem adds some features to the schema.yml:

####Markdown field
Diem allows to declare that a column contains markdown text:

    Post:
      columns:
        body:    { type: clob, extra: markdown }

As body contains markdown, the admin generator will provide a markdown editor. [The SEO synchronizer](page:40#page-metas-and-url:automatic-pages) will also remove formatting when generating metas.

####Link field
Diem allows to declare that a column contains a link:

    Post:
      columns:
        url:    { type: string(255), extra: link }

As url contains a link, the [admin generator](page:70) will allow to drag&drop pages and medias to the url field.

####Media field
This is the best way to add images and files to a model.
Suppose we want to add an image and a downloadable file to the Post model:
~~~
Post:
  columns:
    image:        { type: integer }                // the image is not required
    file:         { type: integer, notnull: true } // the file is required
  relations:
    Image:
      class:      DmMedia
      local:      image
      onDelete:   SET NULL
    File:
      class:      DmMedia
      local:      file
      onDelete:   RESTRICT
~~~
Learn more about Medias on the [Medias documentation page](page:163).

####is_active field
If you give an **is_active** boolean field to your model, Diem knows that it is used to **activate**/**deactivate** the records:
~~~
Post:
  columns:
    is_active: { type: boolean, notnull: true, default:true}
~~~

- Post records which have **is_active** set to **false** do not appear in front application.
- Post pages which Post have **is_active** set to **false** are **secured**.
- In admin application, a batch action is proposed to activate/deactivate multiple Posts at a time.

####Behaviors
Learn about [Diem Doctrine behaviors](page:44).

### config / dm / modules.yml
A full chapter is dedicated to the [module configuration](page:39).

### config / dm / config.yml

Declares the diem specific configuration for your site. By default the configuration is valid, so you don't need to modify it. But if you're interested in tweaking your project, you can.

~~~
all:

  i18n:                                 # internationalization
    cultures:             [ en ]        # Available cultures

  cache:                                # Cache management
    apc:                  true          # (RECOMMENDED) Use Apc if available on current server

  js:
    compress:             true          # (RECOMMENDED) Performance : Minifies javascripts and put them into a single compressed file
    cdn:
      enabled:            false         # Will use cdn to load javascript libraries faster
      lib.jquery:         'http://ajax.googleapis.com/ajax/libs/jquery/1.3/jquery.min.js'
    head_inclusion:       [ ]           # If you want some libraries to be included in the <head> section,
                                        # declare them here. This degrades performances but is required
                                        # by some symfony plugins that embed jQuery code.
                                        # example: [ lib.jquery ]

  css:
    compress:             true          # (RECOMMENDED) Performance : Minifies stylesheets and put them into a single compressed file

  seo:                                  # Search engine optimization configuration
    use_keywords:         true          # Keywords are useless for seo, but you can use them if you want
    truncate:                           # Max length for meta fields. Can not exceed 255 characters
      slug:               255
      name:               255
      title:              80
      h1:                 255
      description:        160
      keywords:           255

  orm:                                  # Doctrine ORM configuration
                                        # More configuration : please use ProjectConfiguration::configureDoctrine method
    identifier_fields:    [ name, title, slug, subject, id ] # Fields used to represent a record with a string
    cache_enabled:        true          # (RECOMMENDED) Use doctrine query cache. No side effect, automatic cache invalidation )
    cache_result_enabled: false         # Use doctrine result cache where query->dmCache() is called
    cache_result_enabled_default: false # Use doctrine result cache on every query ( performance gain, possible issues )

  backup:                               # keep a copy of files modified by Dm code editors
    enabled:              true          # (RECOMMENDED) enable backup

  web_debug:                            # web debug panel configuration
    only_html_response:   true          # will skip web debug panel display on non html response
    config_fast_dump:     true          # use print_r instead of sfYaml::dump to show config in web debug panel. ( ~40x faster )

  toolBar:
    flavour:              blue          # the toolbar flavour. Diem default values: grey, blue, green, brown, black
                                        # You can also set a custom flavour and style #dm_tool_bar.flavour_name in your css path
  media:
    default:              false         # default resource to use when using _media with an empty resource.
                                        # example: /uploads/default.jpg
  security:
    remember_cookie_name:               # defaults to "dm_remember_%project_name%"
    remember_key_expiration_age:        # defaults to 15 days

  locks:                                # whether to enable real time resource locks
    enabled:              <?php echo sfConfig::get('sf_debug') ? "false\n" : "true\n"; ?>
    timeout:              10            # time in seconds to consider a user is no more active on the page

  performance:
    enable_mailer:        true          # Set to false to disable Swift loading: significant performance boost.
                                        # If set to false, you can enable the mailer on demand with dm::enableMailer()
~~~

### config / dm / services.yml
This file controls the Dependency Injection Container.
See the [symfony documentation](http://components.symfony-project.org/dependency-injection/trunk/book/C-YAML-Format) about how to configure it with YAML.

See the [list of the Diem services](page:46) you can tweak.