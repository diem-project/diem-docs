Diem provides natively some of the most popular [Doctrine behaviors](http://www.doctrine-project.org/extensions ""), also called extensions.
Some of them are slightly modified to make use of Diem features, and other ones are completely new and don't exist out of Diem.

##DmBlameable
Fork of [Blameable](http://www.doctrine-project.org/extension/Blameable ""). Add user auditing capabilites to a model: tracks who created and last updated a model.

###Differences with Blameable

- CreatedBy & UpdatedBy are relations to the DmUser model
- by default, the listener uses the authenticated DmUser.

###Usage

####Declaration:
*config/schema.yml*
[code]
Article:
  actAs:
    Dmblameable:
[/code]

Run doctrine migrations, then dm:setup to build models, forms and filters.

####Show in admin list
To show the users in your admin module, add "created_by" and "updated_by" to the list display section:
*apps/admin/modules/article/config/generator.yml*
[code]
      list:
        display:
          - created_by
          - updated_by
[/code]

This behavior is particularly usefull when used with the next one:

##DmVersionable
Extends [Versionable](http://www.doctrine-project.org/documentation/manual/1_2/en/behaviors#core-behaviors:versionable "").

###Differences with Versionable

- fixed issue when used with Sortable behavior

###Usage
####Declaration:
*config/schema.yml*
[code]
Article:
  actAs:
    DmVersionable:
[/code]

Run doctrine migrations, then dm:setup to build models, forms and filters.

#### Browse versions, revert
Nothing more to do, a History button appeared in your model admin forms:
![](media:739)
Click it and view all record versions, with graphical differences. Click "revert to revision x" to cancel changes and go back to an older version.
![](media:740)
When used with DmBlameable, we know who created each version.

##DmSortable
Fork of [Sortable](http://www.doctrine-project.org/extension/Sortable ""). Adds ability to sort your models. Move models up or down and move them before or after another record.

###Differences with Sortable

- uses the "is_active" field:
[code php]
$record->getNext(); // returns the next record with is_active = true
$record->getNext(false); // returns the next record

$record->getPrevious(); // returns the previous record with is_active = true
$record->getPrevious(false); // returns the previous record
[/code]
- added getNextQuery() and getPreviousQuery() methods
- added "new" option to choose if new records are positioned at first or last position. Defaults to first.

###Usage

####Declaration

*config/schema.yml*
[code]
Article:
  actAs:
    DmSortable: { new: last } # new articles are moved to last position
[/code]

Run doctrine migrations, then dm:setup to build models, forms and filters.

####Sort in admin
To enable sorting in your record admin module, add a "sortable" option in the list section:
*apps/admin/modules/article/config/generator.yml*
[code]
      list:
        sortable: true
[/code]
A "Sort" button appears on the list page:
![](media:741)
It leads to a sorting interface based on drag&drop.

##DmGallery
Diem-only behavior. Allows to associate a undefined number of [Medias](page:163) to a record.

###Usage
####Declaration
*config/doctrine/schema.yml*
[code]
Article:
  actAs:
    DmGallery:
[/code]

Run doctrine migrations, then dm:setup to build models, forms and filters.

####Preview in admin list
To display record images in its admin list, add the "dm_gallery" field to the generator list section:
*apps/admin/modules/article/config/generator.yml*
[code]
      list:
        display:
          - dm_gallery
[/code]

####Modify in admin form
To allow writers to add images in the article gallery, add the "dm_gallery" field to the generator form section:
*apps/admin/modules/article/config/generator.yml*
[code]
      form:
        display:
          Gallery: [dm_gallery]
[/code]

####Manipulate the gallery
As the Article model acts as a DmGallery, it has some new methods available:
[code php]
// get a collection of medias related to the article, ordered by position
$medias = $article->getDmGallery();

// get the number of medias related to the article
$nbMedias = $article->getNbMedias();

// get the first media related to the article
$firstMedia = $article->getFirstMedia();

// display all gallery medias
foreach($article->getDmGallery() as $media)
{
  echo _media($media)->size(150, 150);
}
[/code]

### Configuration

#### Change accepted mime types
By default a DmGallery only accepts web images like jpg and png.
If you want to allow using other file types, you can change the form class:
*config/doctrine/schema.yml*
[code]
Article:
  actAs:
    DmGallery:
      formClass: MyDmMediaForm
[/code]
Then in this class, choose the allowed mime types:
*lib/MyDmMediaForm.php*
[code php]
class MyDmMediaForm extends DmMediaForm
{
  public function configure()
  {
    parent::configure();

    // choose mime types allowed
    $this->setMimeTypeWhiteList(array(
      'image/jpeg',
      'image/png',
      'video/x-flv'
    ));
  }
}
[/code]
In this example, both web images and FLV videos are accepted.

##DmTaggable

Makes easy to add tags to a record.
This behavior is provided by [dmTagPlugin](page:157).

##DmCommentable

Makes easy to add comments to a record.
This behavior is provided by [dmCommentPlugin](page:151).

##Others

Here is a non exhaustive list of Doctrine behaviors known to work well with Diem:

- [I18n](http://www.doctrine-project.org/documentation/manual/1_2/en/behaviors#core-behaviors:i18n)
- [NestedSet](http://www.doctrine-project.org/documentation/manual/1_2/en/behaviors#core-behaviors:nestedset)
- [Timestampable](http://www.doctrine-project.org/documentation/manual/1_2/en/behaviors:core-behaviors:timestampable "")
- [Sluggable](http://www.doctrine-project.org/documentation/manual/1_2/en/behaviors#core-behaviors:sluggable)