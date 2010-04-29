In this tutorial, we will take the example of a contact form.
Let's assume you already have a running project.

## Add a contact module

### Declare the data model
We first need to declare the contact model in [the schema.yml file](page:44#configuration-files:config-doctrine-schema-yml).

~~~
Contact:
  actAs:              [ Timestampable ]
  columns:
    name:             { type: string(255), notnull: true }
    email:            { type: string(255), notnull: true }
    topic:            { type: enum, values: ['information', 'request', 'proposal of marriage'] }
    body:             { type: clob }
~~~

### Update database
We will now add the table to the database. Let's use the doctrine migration tool. In your project root directory, run:
~~~
php symfony doctrine:generate-migrations-diff
~~~
You should see :
> *doctrine  generating migration diff*
> *file+     /tmp/doctrine_schema_76085.yml*
> *doctrine  Generated migration classes successfully from difference*

A new doctrine migration class has been generated in /lib/migration/doctrine. Give it a look, then apply the changes :
~~~
php symfony doctrine:migrate
~~~
You should see
> *doctrine  Migrating from version 0 to 1*
> *doctrine  Migration complete*

Now the database contains our new contact table.

[Having troubles with doctrine migrations ?](page:51#the-doctrine-migration-fails)

### Update model
We will now create the contact model. Run :

    php symfony dm:setup
Done.

### Declare the module
Now, add a contact module in [the modules.yml file](page:39) :
~~~
Content:

  ... // your previous modules

  Feedback:              # arbitrary namespace, used in admin content menu

    contact:             # module's name
      components:        # module's components
        form:            # this module has a form action
~~~

### Update the project
As usual, updating project is done with

    php symfony dm:setup

### Play with the contact admin interface

Now, in admin application, you should see the "Contacts" link into the "Content" menu. Our visitors requests will be showed here.

## Add the form on front application

### Create a dedicated page
We will create a dedicated page for our contact form. Click on the "Create page" button, and fill the page's name :
![add the contact page](/uploads/screen/add_contact_page.png)
A slug proposition is generated while you enter the name. Feel free to change it if you want.
All entered values may be changed later.

### Add the form widget
Now that we are on the contact page, we will drag&drop the contact form widget inside a zone :
![dragndrop inside a zone](/uploads/screen/drag_form_widget.png)

*Troubleshooting*

- [Can't drag&drop ?](page:51#the-drag-amp-drop-doesn-t-work-on-the-front)
- [Not seeing the form appear ?](page:51#front-modules-templates-are-not-generated)

Before using the form, you should refresh the page (F5).

The form is now functional, you may fill its fields and see the data appear in the admin contact interface.

## Customize form class

### Validate the email field
As for now, the email fields does not check email validity. Let's fix that.
Open lib/form/doctrine/ContactForm.class.php and add the "$this->changeToEmail('email');" line :
[code php]
class ContactForm extends BaseContactForm
{
  public function configure()
  {
    $this->changeToEmail('email');
  }
}
[/code]

It's just a shortcut to

    $this->validatorSchema['email'] = new sfValidatorEmail($this->validatorSchema['email']->getOptions());

Now the email field must contain a valid email.

Learn more about forms from the [symfony form documentation](http://www.symfony-project.org/forms/1_2/en/).

## Customize the form template

Look into the apps/front/modules/contact/templates/_form.php file :
[code php]
<?php
/*
 * Action for Contact : Form
 * Vars : $form
 */

echo $form;
[/code]

Well, it's sufficient to make it work, but what if we want to customize the way the form is displayed ? Replace the file content with :
[code php]
<?php
/*
 * Action for Contact : Form
 * Vars : $form
 */

// open the form tag with a contact_form css class
echo $form->open('.contact_form');

// open a ul tag
echo _tag('ul',

  // open a li tag and write name label, field and error message inside
  _tag('li', $form['name']->label()->field()->error()).

  // same with email, and add a help message
  _tag('li', $form['email']->label()->field()->help('Will never be published')->error()).

  // change the label text for topic
  _tag('li', $form['topic']->label('What is it about ?')->field()).

  _tag('li', $form['body']->label('Your message')->field())

);

echo $form->renderHiddenFields();

// change the submit button text
echo $form->submit('Send');

// close the form tag
echo $form->close();
[/code]

This template uses [Diem template helpers](page:45)

You may want to learn more about [Diem forms template helpers](page:45#form-helpers)

## Add a confirmation message
When a user has submitted a form successfully, it's a good practice to thank him.

In the contact actions,
apps/front/modules/contact/actions/actions.class.php,
we will add the line :
**$this->getUser()->setFlash('contact_form', true);**

[code php]
<?php
/**
 * Contact actions
 */
class contactActions extends dmFrontModuleActions
{

  public function executeFormWidget(dmWebRequest $request)
  {
    $form = new ContactForm();

    if ($request->isMethod('post') && $form->bindAndValid($request))
    {
      $form->save();

      $this->getUser()->setFlash('contact_form_valid', true);

      $this->redirectBack();
    }

    // pass the form to the component using the form manager
    $this->forms['Contact'] = $form;
  }

}
[/code]

Then, in the template, we will add the congratulation message.
apps/front/modules/contact/templates/_form.php :
[code php]
<?php
/*
 * Action for Contact : Form
 * Vars : $form
 */
if ($sf_user->getFlash('contact_form_valid'))
{
  echo _tag('p.congratulation', 'Thank you for your message');
}

// ...the form
[/code]