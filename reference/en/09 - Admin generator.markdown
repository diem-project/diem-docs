Diem uses the excellent [symfony admin generator](http://www.symfony-project.org/reference/1_4/en/06-Admin-Generator). But with some additions.

##generator.yml builder
Diem is smart enough to write the generator.yml file for you. When you generate a new module with

    php symfony dm:setup

The admin module is created, with a quite good generator.yml.

Later, if you add fields to the model, Diem will not add the fields to the generator.yml, because it may override your changes. So, if you want Diem to regenerate a generator.yml from the current model, use

    php symfony dmAdmin:generate --clear=myModule

To customize the generator.yml, please see the [symfony admin generator documentation](http://www.symfony-project.org/reference/1_4/en/06-Admin-Generator) and the [Diem added configuration](#added-configuration).

## Override plugin modules
If you override a plugin model in your schema.yml, the admin module will *not* be generated in your project. You have to copy manually the plugin generator.yml:
*dmGreatPlugin/modules/dmGreatModuleAdmin/config/schema.yml*
into your project, in
*apps/admin/modules/dmGreatModuleAdmin/config/schema.yml*
And add the missing fields manually, as you would do in a normal symfony application.

##Added features
Diem admin generator comes with more features than the symfony one.

- history interface to manage [records versions](#versioning)
- google-like search
- advanced search with symfony filters
- max-per-page selector
- sort on partial ( if the partial has the same name than a database field )
- sort on foreign keys
- [markdown editor with droppable links and medias](page:99), and ajax preview
- Save, Save and Add, Save and Back to list, Save and Next buttons
- fast navigation between objets ( Previous - Next )
- Bread crumb
- Automatic sort interface with jQuery UI Sortable
- Automatic batch actions : delete, activate, deactivate
- Automatic fixtures called "Loremization" ([see Tutorial #1: create dummy posts](page:48#rest-a-bit-and-have-some-fun:create-some-dummy-posts))
- Foreign objects made automatically clickable
- Drag & drop pages and medias to form fields

Diem admin interface is heavily inspired by the [Total Usability Blog](http://totalusability.posterous.com/) and the Gmail interface.

- [Filter forms are evil](http://totalusability.posterous.com/forms-are-evil-1-filter-forms)
- [If it works like email, usability is not a problem](http://totalusability.posterous.com/if-it-works-like-email-usability-is-not-a-pro)

##Added configuration

###Fields

~~~
config:
  fields:
    url:
      is_link:      true
    title:
      is_big:       true
    body:
      markdown:     true
~~~

####is_link
When a field is marked as *is_link*, it becomes droppable.

- The user can drag&drop pages from the left PAGES panel inside, to link to an internal page.
- The user can drag&drop medias from the right MEDIA panel inside to generate a download link.
- The user can write a full url, to link to an external site.

>**TIP**
>When a database field contains a link, it's better to declare it in the [schema.yml](page:44#configuration-files:config-doctrine-schema-yml:diem-additions-to-schema-yml:link-field). This way, not only the admin generators knows how to deal with it, but the whole project too.

####is_big
Diem admin forms are displayed in two columns.
When a field is marked as *is_big*, it uses the two columns.

####markdown
When a textarea is marked as *markdown*, it is transformed into a markdown editor with an ajax preview.

>**TIP**
>When a database field contains markdown text, it's better to declare it in the [schema.yml](page:44#configuration-files:config-doctrine-schema-yml:diem-additions-to-schema-yml:markdown-field). This way, not only the admin generators knows how to deal with it, but the whole project too.

## Sort on partials

With symfony only, we can't sort list results by a partial column.
With Diem it's made possible **if the partial has the same name than a database field**.

For example suppose we have a "Product" model, with a "price" field.
We will create the "_price.php" template to show exclusive of taxes and inclusive of taxes prices.
When clicking on the list "_price" column, list will be sorted using the "price" field.

## Show and edit Medias

Show the [admin generator section of the Medias documentation](page:163#record-medias-in-admin).

##Versioning
To add versioning to your model, declare which fields are versionned on the
**config/schema.yml**
~~~
Post:
  actAs:
    DmVersionable:
~~~
If your model is translatable, use Doctrine nested behaviors to enable versioning on i18n fields:
~~~
Post:
  actAs:
    I18n:
      fields:         [ title, body ]
      actAs:
        DmVersionable:
~~~

>Versioning on i18n fields [won't work with PostGreSQL](page:51#models-which-translation-is-versionned-don-t-work-with-postgresql) due to a known [Doctrine bug](http://www.doctrine-project.org/jira/browse/DC-135)

Diem will automatically add an **history** button on the post admin form. In the history interface, you can:

- browse the post versions
- see the changes between two versions with visual diffs
- revert the post to an older version

## Filtering dates

### Special widget
For filtering dates Diem comes with a special widget, more user friendly, in which you choose between "Today", "Past 7 days", "This month" and "This year".

### symfony common date widget
You can also use the default symfony date filter widget, less user-friendly but more precise.

In the configure function of the form filter:
[code php]
  public function configure()
  {
    $this->widgetSchema['created_at'] = new sfWidgetFormFilterDate(array(
      'from_date' => new sfWidgetFormDate(),
      'to_date' => new sfWidgetFormDate(),
      'with_empty' => true
    ));
    $this->validatorSchema['created_at'] = new sfValidatorDateRange(array(
      'required' => false,
      'from_date' => new sfValidatorDate(array('required' => false)),
      'to_date' => new sfValidatorDate(array('required' => false))
    ));
  }
[/code]

### Diem datepicker widget
Also it's possible to use Diem datepicker widget, but it's a little bit tricky because by default javascript don't work in ajax filters.

In the configure function of the form filter:
[code php]
  public function configure()
  {
    $this->widgetSchema['created_at'] = new sfWidgetFormFilterDate(array(
      'from_date' => new sfWidgetFormDmDate(array(), array("style" => "float:none")),
      'to_date' => new sfWidgetFormDmDate(array(), array("style" => "float:none")),
      'template' => '%from_date% - %to_date% (from - to)',
      'with_empty' => true
    ));
    $this->validatorSchema['created_at'] = new sfValidatorDateRange(array(
      'required' => false,
      'from_date' => new dmValidatorDate(array('required' => false)),
      'to_date' => new dmValidatorDate(array('required' => false))
    ));
  }
[/code]

You need to include the libraries of the datepicker in the view.yml:
[code]
/apps/admin/config/view.yml
  stylesheets:
    - lib.ui-datepicker

  javascripts:
    - admin
    - lib.ui-i18n
    - lib.ui-datepicker
[/code]

And in the admin.js, add a line with the live() function for enabling javascript in ajax filters:
[code]
/web/js/admin.js

$(function(){
	$('input.datepicker_me').live('focus', function() {
		$(this).datepicker();
	});
});
[/code]

##Custom filters

### Filtering with a choice widget
Many time we define types of some field:
[code php]
class PersonTable extends myDoctrineTable
{
  static public $genders = array(
    1 => 'Male',
    2 => 'Female',
  );
}
[/code]

In the form filter, the autogenerated widget is and input, but we would like to use a choice, so we change the widget:

[code php]
    public function configure()
  {
    //add empty choice
    $gender_choices = array(null => null) + PersonTable::$genders;

    $this->widgetSchema['gender'] = new sfWidgetFormChoice(array(
          'choices' => $gender_choices
    ));
    $this->validatorSchema['gender'] = new sfValidatorChoice(array(
          'required' => false,
          'choices' => array_keys($gender_choices)
    ));
  }
[/code]