##Module based plugin

In this example we will create a simple blog plugin, intelligently called "dmSimpleBlogPlugin".

First, enable it in
config/ProjectConfiguration.class.php
[code php]

class ProjectConfiguration extends dmProjectConfiguration
{
  public function setup()
  {
    parent::setup();

    $this->enablePlugins(array(
      'dmSimpleBlogPlugin'
    ));

    // ...
[/code]

###Plugin structure
Let's create a minimal plugin structure in our project:
![simple blog plugin structure](media:656)

####schema.yml
[code]
DmSimpleBlogPost:
  actAs:              [ Timestampable, Sortable, DmVersionable ]
  columns:
    name:             { type: string(255), notnull: true }
    resume:           { type: string(255) }
    text:             { type: clob, extra: markdown }
    is_active:        { type: boolean, notnull: true, default: true }
    created_by:       { type: integer }
  relations:
    Author:
      class:          DmUser
      local:          created_by
      foreignAlias:   DmSimpleBlogPosts
      onDelete:       SET NULL

DmSimpleBlogComment:
  actAs:              [ Timestampable ]
  columns:
    post_id:          { type: integer, notnull: true }
    name:             { type: string(255), notnull: true }
    text:             { type: clob }
    is_active:        { type: boolean, notnull: true, default: true }
  relations:
    Post:
      class:          DmSimpleBlogPost
      local:          post_id
      foreignAlias:   Comments
      onDelete:       CASCADE
[/code]
It's a good pratice to prefix the plugin models with **Dm%PluginName%**.

####modules.yml
[code]
Content:

  "Simple Blog":

    dmSimpleBlogPost:
      name:                   Blog Post
      page:                   true
      components:
        list:
        show:

    dmSimpleBlogComment:
      name:                   Blog Comment
      components:
        listByBlogPost:
          filters:            [ dmSimpleBlogPost ]
        form:
[/code]
It's a good pratice to prefix the plugin modules with **dm%PluginName%**.

###Code generation

[code]
php symfony doctrine:generate-migrations-diff

php symfony doctrine:migrate

php symfony dm:setup
[/code]

Now the plugin is ready.
Admin and front modules have been created in the plugin dir. Customize them as you do for a normal project.