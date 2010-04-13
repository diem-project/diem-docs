##What's a Media
Medias are files that belong to the website content.
A product photo, a screenshot, a PDF document or a ZIP package are Medias.
A PHP file, a stylesheet or a background image are **not** Medias.

In Diem, each Media is represented by a DmMedia record, stored in the dm_media table. The files are stored in sfConfig::get('sf_uploads_dir'), which is generally web/uploads.

##Bind a Media to a record
Let's suppose we want our Article model to have an image. The easiest way to do it: add a DmMedia relation to the Article model.
*config/schema.yml*
[code]
Article:
  columns:
    image_id:     { type: integer, notnull: true } # the image is required
  relations:
    Image:
      class:      DmMedia
      local:      image_id
      onDelete:   RESTRICT # the image can not be deleted, unless if the article is deleted before
[/code]

If we want the article to have another Media, let's say a downloadable slide:
*config/doctrine/schema.yml*
[code]
Article:
  columns:
    image_id:     { type: integer, notnull: true }  # the image is required
    slide_id:     { type: integer, notnull: false } # the slide is not required
  relations:
    Image:
      class:      DmMedia
      local:      image_id
      onDelete:   RESTRICT # the image can not be deleted, unless if the article is deleted before
    Slide:
      class:      DmMedia
      local:      slide_id
      onDelete:   SET NULL # the slide can be deleted
[/code]

Run Doctrine migrations, then dm:setup to build the model and the forms.

##Secure the form
The first thing to do is to ensure that the files that will be uploaded are what we expect them to be. We don't want someone to upload, let's say, a PHP file instead of an image...
So, let's configure the admin article form to only accepts JPG and PNG files as images.
We will use the **createMediaFormForImageId** method to modify the image DmMedia form just before it is embedded.
*apps/admin/modules/article/lib/ArticleAdminForm.php*
[code php]
class ArticleAdminForm extends BaseArticleForm
{
  protected function createMediaFormForImageId()
  {
    // get the DmMedia form
    $form = parent::createMediaFormForImageId();

    // choose mime types allowed
    $form->setMimeTypeWhiteList(array(
      'image/jpeg',
      'image/png'
    ));

    return $form;
  }
[/code]

>**About the setMimeTypeWhiteList shortcut**
> This new method is just a handy shortcut to:
>~~~
>$form->getValidatorSchema->offsetGet('file')->setOption('mime_types', $mimeTypes);
>~~~

To restrict slide files, use the **createMediaFormForSlideId()** method.

##Record Medias in admin

###Preview on list page
We want to be able to see the article images in admin list. Just add a "image_id" field in admin generator list section:
*apps/admin/modules/article/config/generator.yml*
[code]
      list:
        display:
          - image_id
[/code]
We use the field name "image_id", not the relation name. Diem knows "image_id" is the local key of a DmMedia relation, so instead of showing the Media id, it will display a resized image preview.

###Upload on form page
We want to upload a new image on article admin form. To show the DmMedia embedded form, just add "image_id_form" in admin generator form section:
*apps/admin/modules/article/config/generator.yml*
[code]
      form:
        display:
          Image: [image_id_form]
[/code]
The embedded form name is composed by the field name "image_id" and a suffix "_form".

###Preview on form page
It's good to have the preview of the uploaded image. Diem makes it easy: just add "image_id_view" in admin generator form section:
*apps/admin/modules/article/config/generator.yml*
[code]
      form:
        display:
          Image: [image_id_form, image_id_view]
[/code]
Diem knows "image_id" is the local key of a DmMedia relation. The field "image_id_view" will display a resized preview of the image, with usefull informations about the file.

##Record Medias in front

###Show a record Media

####Render an image
So we have articles with images. We want to show the article images in article front templates.
Diem provides a powerful helper: **_media()**. I displays Medias and allows to resize them on the fly.

*apps/front/modules/article/templates/_show.php*
[code php]
echo _media($article->Image)->size(300, 200);
[/code]
Learn more about [**_media()** nice features](page:45#_media-create-a-media).

####Download a file
We will also propose to download the article slide. This time, we will use the **_link()** helper:

*apps/front/modules/article/templates/_show.php*
[code php]
echo _link($article->Slide);
[/code]
Learn more about [**_link()** nice features](page:45#_link-create-a-link).

###Upload a record Media
Sometimes we want to allow visitors to upload files from the front. Let's assume we use the [dmContactPlugin](page:131). We already have a DmContact model, we need to add it a Document relation:
*config/doctrine/schema.yml*
[code]
DmContact:
  columns:
    document_id:      { type: integer }
  relations:
    Document:
      class:          DmMedia
      local:          document_id
      onDelete:       SET NULL
[/code]
Run doctrine migrations, then dm:setup to build models and forms.

####Secure the form
The first thing to do is to ensure that the files that will be uploaded are what we expect them to be. We don't want someone to upload, let's say, a PHP file...
So, let's configure the contact form to only accepts PDF and text documents.
We will use the **createMediaFormForDocumentId** method to modify the contact DmMedia form just before it is embedded.
*lib/form/doctrine/dmContactPlugin/DmContactForm.class.php*
[code php]
class DmContactForm extends PluginDmContactForm
{
  protected function createMediaFormForDocumentId()
  {
    // get the DmMedia form
    $form = parent::createMediaFormForDocumentId();

    // choose mime types allowed
    $form->setMimeTypeWhiteList(array(
      'application/pdf',
      'application/msword', // .doc
      'application/vnd.oasis.opendocument.text' // .odt
    ));

    return $form;
  }
[/code]

>**About the setMimeTypeWhiteList shortcut**
> This new method is just a shortcut to:
>~~~
>$form->getValidatorSchema->offsetGet('file')->setOption('mime_types', $mimeTypes);
>~~~

#### Render the form
The embedded DmMedia form that holds the upload input is called "document_id_form". We can render it quite easily in the contact form:
*apps/front/modules/dmContact/templates/_form.php*
[code php]
echo $form['document_id_form'];
[/code]

#### Change form fields
By default, a DmMedia form contains four fields: file, legend, author and license. Typically, on a front form, we don't want to bother the visitor with all these fields.
We will keep only the file field, and unset the others, in the DmContactForm.
*lib/form/doctrine/dmContactPlugin/DmContactForm.class.php*
[code php]
class DmContactForm extends PluginDmContactForm
{
  protected function createMediaFormForDocumentId()
  {
    // get the DmMedia form
    $form = parent::createMediaFormForDocumentId();

    // choose mime types allowed
    $form->setMimeTypeWhiteList(array(
      'application/pdf',
      'application/msword', // .doc
      'application/vnd.oasis.opendocument.text' // .odt
    ));

    // remove unnecessary fields
    unset($form['legend'], $form['author'], $form['license']);

    return $form;
  }
[/code]

##Multiple Medias
What about records having not one image, but a undefined number of images? For example a product in a shop can have several photos.
Diem provides a solution as a Doctrine Behavior called DmGallery.

Learn more about [DmGallery in Diem Doctrine behaviors documentation](page:164#dmgallery).