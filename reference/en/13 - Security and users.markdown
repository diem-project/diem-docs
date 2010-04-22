Diem adds a thin layer on the top of [symfony security system](http://www.symfony-project.org/reference/1_4/en/08-Security).

## User management

dmUserPlugin is a fork of [sfDoctrineGuardPlugin](http://www.symfony-project.org/plugins/sfDoctrineGuardPlugin). It provides users, permissions and groups.

###Users
A user is an instance of DmUser.  It has a name and an email, and can be associated to permissions and groups.

A builtin interface is provided in admin application to manage users. In the upper tool bar, click System->Security->Users.

####Add fields and relations to the user model
Diem doesn't use an external Profile model to store extra user informations. It's way simpler to add directly what you need to the DmUser model.
Let's say you want your user to have a description and a photo. Just add the fields and relations in your [config/doctrine/schema.yml](page:44#configuration-files:config-doctrine-schema-yml):
~~~
DmUser:
  columns:
    description:   { type: clob, extra: markdown }
    photo:         { type: integer }
  relations:
    Photo:
      class:       DmMedia
      local:       photo
      onDelete:    RESTRICT
~~~
Learn more about Diem [schema.yml](page:44#configuration-files:config-doctrine-schema-yml).

Then, run your doctrine migrations and the dm:setup task.

>**Update the user admin interface**
>Plugin admin modules are not generated in your project. So this time you need to copy yourself
>*diem/dmCorePlugin/plugins/dmUserPlugin/modules/dmUserAdmin/config/generator.yml*
>into your project, in
>*apps/admin/modules/dmUserAdmin/config/generator.yml*
>And add manually the fields:
>~~~
      list:
        display:
          - description
          - photo
>~~~
>
>~~~
>      form:
>        display:
>          "My fields": [ photo_form, photo_view, description ]
>~~~

###Permissions
A permission is an instance of DmPermission. When associated to a user, it defines what he is allowed to do.

A builtin interface is provided in admin application to manage permissions. In the upper tool bar, click System->Security->Permissions.

###Groups
A group is an instance of DmGroup. When a user is associated to a group, the user get all the group's permissions.

A built-in interface is provided in admin application to manage groups. In the upper tool bar, click System->Security->Groups.

##Secure a page
A secured page can only be seen by authenticated users.
When a non-authenticated user tries to access a secured page, he is forwarded to the main.login page.
You are responsible for creating the Login page content ( message, login form... ).

###Manual pages
To secure a manual page, go to the page and edit it by clicking on the "Edit page" button. A dialog appears. In the "Publication" tab, click the "Requires authentication" checkbox.

###Automatic pages
For pages that represent a record ( e.g. blog post page ), you can use the Post model [is_active field](page:44#configuration-files:config-doctrine-schema-yml:is_active-field). It allows you to activate/deactivate the posts from admin interface. Deactivated post pages are secured, and deactivated posts no more appear in post lists.

##Secure an action
The [symfony way](http://www.symfony-project.org/reference/1_4/en/08-Security) to secure an action works with Diem.

##Allow users to signin

When a non-authenticated user tries to access a secured page, he/she is forwarded to the signin page.
The signin page is generated when the project is created, the default url is "/signin". It contains a dmUser/signin widget that allows user to signin to the application if he/she already has an account.

###Disable signin on front application
You can remove the dmUser/signin widget and replace it with a content/text widget for example, that would say the page is secured and can not be accessed.

###Customize the signin template
The default template is provided by dmUserPlugin. To replace it, copy
*dmCorePlugin/plugins/dmUserPlugin/modules/dmUser/templates/_signin.php*
to
*apps/front/modules/dmUser/templates/_signin.php*
Then modify it.

###Customize the signin form
Copy
*dmCorePlugin/plugins/dmUserPlugin/lib/form/DmSigninFrontForm.php*
to
*apps/front/lib/form/DmSigninFrontForm.php*
Then modify it.

###Customize the redirection when a user signs in
Create the dmUser actions class in your project, and replace the redirectSignedInUser method.
*apps/front/modules/dmUser/actions/actions.class.php*
[code php]
require_once sfConfig::get('dm_core_dir').'/plugins/dmUserPlugin/modules/dmUser/lib/BasedmUserActions.class.php';

class dmUserActions extends BasedmUserActions
{
  protected function redirectSignedInUser(dmWebRequest $request)
  {
    $redirectUrl = $this->getUser()->getReferer($request->getReferer());

    $this->redirect('' != $redirectUrl ? $redirectUrl : '@homepage');
  }
[/code]

##Allow users to self-register
The front "Add" menu already contains a user/register widget. It allows the user to create an account on your site.
On the signin page, or any other page, just drag&drop a user/register widget.

###Customize the register template
The default template is provided by dmUserPlugin. To replace it, copy
*dmCorePlugin/plugins/dmUserPlugin/modules/dmUser/templates/_form.php*
to
*apps/front/modules/dmUser/templates/_form.php*
Then modify it.

###Customize the register form
The form class already exists in your project, just modify it.
*lib/form/doctrine/dmUserPlugin/DmUserForm.classs.php*

###Customize the register action
Create the dmUser actions class in your project, and replace the executeFormWidget method.
*apps/front/modules/dmUser/actions/actions.class.php*
[code php]
require_once sfConfig::get('dm_core_dir').'/plugins/dmUserPlugin/modules/dmUser/lib/BasedmUserActions.class.php';

class dmUserActions extends BasedmUserActions
{

  /**
   * Handle dmUser/form form validation and creates the user account, then authenticates the user
   */
  public function executeFormWidget(dmWebRequest $request)
  {
    $form = new DmUserForm();

    if ($request->isMethod('post') && $request->hasParameter($form->getName()))
    {
      $data = $request->getParameter($form->getName());

      // if the form uses captcha, include the additional data
      if($form->isCaptchaEnabled())
      {
        $data = array_merge($data, array('captcha' => array(
          'recaptcha_challenge_field' => $request->getParameter('recaptcha_challenge_field'),
          'recaptcha_response_field'  => $request->getParameter('recaptcha_response_field'),
        )));
      }

      $form->bind($data, $request->getFiles($form->getName()));

      if ($form->isValid())
      {
        $user = $form->save();

        $this->getUser()->signin($user);

        $this->redirectRegisteredUser($request);
      }
    }

    // pass the form to the component
    $this->forms['DmUser'] = $form;
  }
}
[/code]

###Customize the redirection when a user registers
Create the dmUser actions class in your project, and replace the redirectRegisteredUser method.
*apps/front/modules/dmUser/actions/actions.class.php*
[code php]
require_once sfConfig::get('dm_core_dir').'/plugins/dmUserPlugin/modules/dmUser/lib/BasedmUserActions.class.php';

class dmUserActions extends BasedmUserActions
{
  protected function redirectRegisteredUser(dmWebRequest $request)
  {
    $this->redirect($request->getReferer());
  }
[/code]

##Allow users to modify their personal informations
In this example, we will allow an authenticated user to modify its email address.
It will require a form object, an action method to handle the submitted form, a component method to pass the form to the partial, and a partial to display the form.
###Create the form
We will create the DmUserEditEmail form and make it extend the existing DmuserForm.
*apps/front/lib/DmUserEditEmailForm.php*
[code php]
class DmUserEditEmailForm extends DmUserForm
{
  public function configure()
  {
    parent::configure();

    $this->useFields(array('email'));
  }
}
[/code]
###Override the dmUser module directory
We will create a dmUser module in our front application, that extends the existing dmUser module of dmUserPlugin. Create the following dirs and files in *apps/front/modules*:
[code]
dmUser
  actions
    actions.class.php
    components.class.php
  templates
    _editEmail.php
[/code]
###Declare the dmUser/editEmail component
Let's tell Diem the dmUser module has a editEmail component, to transform it to a droppable widget.
*config/dm/modules.yml*
[code]
Project:

  Global:

    dmUser:
      components:
        editEmail:
[/code]
###Instantiate, handle and display the form
####Action
Form handling must always be done in actions. Because it can result on database modifications and HTTP redirections, handling a form must be done before rendering anything. That's the purpose of symfony actions.
*apps/front/modules/dmUser/actions/actions.class.php*
[code php]
// load the class we extend
require_once dm::getDir().'/dmCorePlugin/plugins/dmUserPlugin/modules/dmUser/lib/BasedmUserActions.class.php';

// we extend the dmUserPlugin existing actions
class dmUserActions extends BasedmUserActions
{
  // will be called for any page containing a dmUser/editEmail widget
  public function executeEditEmailWidget(sfWebRequest $request)
  {
    // secure the page if no user is authenticated
    $this->forwardSecureUnless($dmUser = $this->getUser()->getDmUser());

    // create the form with the authenticated user
    $form = new DmUserEditEmailForm($dmUser);

    // if the form has been submitted and is valid
    if ($request->hasParameter($form->getName()) && $form->bindAndValid($request))
    {
      $user = $form->save();

      return $this->redirect($request->getReferer());
    }

    // pass the form to the component
    $this->forms['DmUserEditEmail'] = $form;
  }

}
[/code]
Let's explain this code.
[code php]
public function executeEditEmailWidget(sfWebRequest $request)
[/code]
When displaying a Diem page, the only symfony action called is dmFront/page. This action renders the page.
So, when we want to use actions in a page, we use the widgets in the page to define which actions are called. A page which contains the **dmUser/editEmail** widget will run the **executeEditEmailWidget** action of the **dmUser** module.
Doing so allows to run several actions for one page, and have the same action reusable for several pages. All pages that  contain the dmUser/editEmail widget will call this action.
[code php]
$this->forms['DmUserEditEmail'] = $form;
[/code]
We need to create the form in an action to handle it before anything is rendered. But the form is not rendered by this action template, but by a component partial.
So we need a way to pass the form to the component. To do so, we use the form manager, represented by **this->forms**. It is an object acting as an array, available as well in actions and components. By assigning **$this->forms['DmUserEditEmail']** in this action, we make it available in the component.

####Component
As the form creation and handling is made in the action, the only task of the component is passing it to the template.
*apps/front/modules/dmUser/actions/components.class.php*
[code php]
// load the class we extend
require_once dm::getDir().'/dmCorePlugin/plugins/dmUserPlugin/modules/dmUser/lib/BasedmUserComponents.class.php';

// we extend the dmUserPlugin existing components
class dmUserComponents extends BasedmUserComponents
{

  public function executeEditEmail()
  {
    // get the form created in the action
    $this->form = $this->forms['DmUserEditEmail'];
  }

}
[/code]
Here we use the same form_manager to get the form created in the action: **$this->forms['DmUserEditEmail']**.

####Template
The templates receives $form from the component, and displays it:
*apps/front/modules/dmUser/templates/_editEmail.php*
[code php]
echo $form;
[/code]

###Drop the dmUser/editEmail widget
On the front tool bar, click the refresh project button to clear the cache and apply all our modifications.
The last and easier step is of course to drag&drop our new dmUser/editEmail widget from the "Add" menu to a page.

## Add the "Forgot password" feature
Since 5.1, Diem provides ability to easily send new passwords to your users. The user provides an email, and if this email exists, an new password is generated and sent to this email.
You need a server that is able to send mails to use this feature.

### Create the "Forgot password" page
From the front tool bar, create a new page that will contain the forgot password form.
![](media:776)
Save the page, then click the "Edit page" button of the front tool bar to change the page action to "forgotPassword". This will allow to create a link to this page, later, with _link('main/forgotPassword')
![](media:777)

###Add the dmUser/forgotPassword widget
Find the dmUser/forgotPassword widget in the front "Add" menu, drag it and drop it to the page content.
As this widget contains a form, you need to reload the page to make it appear.

###Add a link to this page
You want to add a link to this page to let the user request a new password. Create the link in one of your templates this way:
[code php]
echo _link('main/forgotPassword')->text('Forgot your password?');
[/code]

###Customize the mail sent
Find the "dm_user_forgot_password" mail template in Tools->Mail templates and customize it.