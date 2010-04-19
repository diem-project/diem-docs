To send mails, Diem obviously uses symfony 1.4 default mailer: Swift.
So you can use it the same way you would do with symfony.

But Diem also offers additional features on top of symfony mailer.

## Mail templates
When sending a mail with symfony, you generally write the mail body in a file template.
The mail subject is also frequently hard-coded in an action, as well as "from" and "to" emails.
The problem is that the developper is responsible for writing and maintaining all these values.
Each time the customer wanna change the mail body, he depends on the developper.

Diem features a Mail template module.
The goal is to let a non-developper manage sent mails, by moving all mail attributes from the code to the database.

### Send a mail using a mail template

Mails are generally sent from a symfony action.
Suppose you want to send a mail to a registered user when (s)he signs up a petition.

[code php]
$this->getService('mail')                   // get a dmMail instance
->setTemplate('sign_petition_confirmation') // choose a DmMailTemplate
->addValues(array(                          // add values to populate the template
  'user_name'       => $user->username,
  'user_email'      => $user->email,
  'petition_name'   => $petition->name
))
->send();                                   // send the mail using Swift Mailer
[/code]

The first time your run this code, the "sign_petition_confirmation" mail template does not exist, and gets created automatically.

### Configure the template

Now you need to modify it in Admin->Tools->Mail templates. A Mail template has the following fields:

#### Name
The unique name of the template, used in your PHP code to identify it.
Ex: "sign_petition_confirmation"

#### Description
Short description to help you remember what this mail template is used to.
Ex: "Congrat a user who just signed up a petition"

#### Active
Whether to send emails that use this template or not.

#### Subject
The one-line mail subject.
Ex: "Hello, dear %username%"

#### Body
The mail body.
Ex: "Thanks for signing the petition %petition_name%!"

#### From Email
The "from" header of the mail.
Ex: "webmaster@mysite.com"

#### To Email
The email, or list of emails, that will receive the mail.
Ex: "%user_email%"

You can use the variables declared in the PHP code: %user_name%, %user_email%, %petition_name%.
Other advanced fields are available to send carbon copies and/or customize the mail headers.

### More ways to pass values

In the previous example, we pass values to the template with a simple PHP array:

[code php]
->addValues(array(                          // add values to populate the template
  'user_name'       => $user->username,
  'user_email'      => $user->email,
  'petition_name'   => $petition->name
))
/*
 * Values available in the template:
 * %user_name%, %user_email%, %petition_name%
 */
[/code]

You can also use a Doctrine record or a Doctrine form.
In this case, the table columns are used as keys, and the object values as values.

[code php]
->addValues($user); // $user is an instance of the DmUser model, which extends DoctrineRecord
/*
 * Values available in the template:
 * %username%, %email%, %is_active%, ...
 */
[/code]

You can call the addValues() method several times, and pass it a prefix as a second argument:

[code php]
->addValues($user, 'user_')
->addValues($petition, 'petition_')
->addValues(array(
  'petition_url' => $this->getHelper()->link($petition)->getAbsoluteHref()
))
/*
 * Values available in the template:
 * %user_name%, %user_email%, %user_is_active%, %petition_name%, %petition_text%, %petition_url%
 */
[/code]