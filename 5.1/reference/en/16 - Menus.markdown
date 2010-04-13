The menu service is designed to help you building and rendering your menus.

It's inspired by [sfSympalMenu](http://www.sympalphp.org/documentation/1_0/book/menus/en "").

On a component:
[code php]
// Get a new menu from the service container
$this->menu = $this->getService('menu');

// Build the menu
$this->menu
->addChild('Home', '@homepage')->end()
->addChild('Contact', 'contact/form')->end()
->addChild('Blog', 'articles/list')->end()
->addChild('Sites')
  ->addChild('Diem', 'http://diem-project.org')->end()
  ->addChild('Symfony', 'http://symfony-project.org')->end()
->end();
[/code]

In the template
[code php]
// Render the menu
echo $menu;
[/code]

Produced HTML
[code php]
<ul>
  <li class="first"><a class="link" href="/">Home</a></li>
  <li><a class="link" href="/contact-us">Contact us</a></li>
  <li><a class="link" href="/blog">Blog</a></li>
  <li class="last">Sites
    <ul>
      <li class="first"><a class="link" href="http://diem-project.org">Diem</a></li>
      <li class="last"><a class="link" href="http://symfony-project.org">Symfony</a></li>
    </ul>
  </li>
</ul>
[/code]

## The addChild method
This method returns the added child.
[code php]
$child = $menu->addChild('New child');
// $child is 'New child'
[/code]
This allows to configure the child in a chainable way:
[code php]
$child = $menu->addChild('Other child')->secure(true)->liClass('big');
// $child is 'Other child'
[/code]
To get back the menu, use the end() method.
It's a shortcut to getParent() and simply returns the parent of the current menu.
[code php]
$menu = $menu->addChild('Other child')->end();
// $menu is the original $menu
[/code]
This allows to add many childs in a chainable way:
[code php]
$menu
->addChild('New child')->end()
->addChild('Other child')->secure(true)->liClass('big')->end();
[/code]
And to create nested menus easily;
[code php]
$menu
->addChild('Rock bands')            // start rock bands menu
  ->addChild('Led Zeppelin')->end()
  ->addChild('Deep Purple')->end()
->end();                            // end rock bands menu
[/code]

## Menu attributes
### Link
You can pass a link with the second argument of the addChild method
[code php]
$menu->addChild('Home', '@homepage');
[/code]
Or with the link method
[code php]
$home = $menu->addChild('Home');
$home->link('@homepage');
[/code]

As the menu service uses [Diem Links](page:45#_link-create-a-link) internally, you can pass both internal or externals uri, records, pages and medias to a menu item.

Exemple: create a product list menu
[code php]
// Menu item for the product list: use product/list as a link
$productListMenu = $menu->addChild('Products', 'product/list');

foreach($products as $product)
{
  // Menu item for a product: use $product as a link
  $productListMenu->addChild($product->name, $product);
}
[/code]

###Label
The label is the text that is displayed in a menu item. It is automatically translated if needed.
[code php]
// Text label
$menuItem->label('Back to homepage');

// HTML label
$menuItem->label('<h2>My header label</h2>');
[/code]

## Menu options

###secure
A secured menu item will only be displayed for authenticated users.
[code php]
$menuItem->secure(true);
[/code]

###not authenticated
A not_authenticated menu item will only be displayed for non authenticated users.
[code php]
$menuItem->notAuthenticated(true);
[/code]

###credentials
If you set credentials to a menu item, it will only be displayed to the users having at least one of the required credentials.
[code php]
// One credentials
$menuItem->credentials('admin');

// Many credentials
$menuItem->credentials(array('admin', 'content'));

// Same as previous
$menuItem->credentials('admin, content');
[/code]

### ul class
Defines the class appplied to the ul tag.
[code php]
$menuItem->ulClass('my_class another_class');
[/code]
### li class
Defines the class appplied to the li tag.
[code php]
$menuItem->liClass('my_class another_class');
[/code]
### show id
If set to true, an automatic HTML id is added to each li tag, based on the item name.
Defaults to false.
[code php]
$menuItem->showId(true);
[/code]
### translate
If set to true, the label will automatically be translated.
Defaults to true.
[code php]
$menuItem->translate(true);
[/code]
### show children
If set to true, the item children are displayed.
Defaults to true.
[code php]
$menuItem->showChildren(true);
[/code]

## Curent and parent pages

Menu items that target the current page get a "dm_current" class on the li and a tag.

Menu items that target a parent page get a "dm_parent" class on the li and a tag.

Learn more about current and parent classes, and how to change them, on the [_link documentation](page:45#_link-create-a-link:links-to-the-current-page).

## Navigate in menu hierarchy
[code php]
// get the menu item parent
$parent = $menuItem->getParent();

// get the menu root
$root = $menuItem->getRoot();

// get the menu children
$children = $menuItem->getChildren();

// get the first or last child
$child = $menuItem->getFirstChild();
$child = $menuItem->getLastChild();
[/code]

## Use your own menu class
If you want to **override methods** from the dmMenu class, or if you want to **move the menu creation to a dedicated class**, you may want to use your own menu class.
The getService method accepts a second argument to specify a different class to use.

Create a myMenu class in _apps/front/lib/myMenu.php_
[code php]
class myMenu extends dmMenu
{
  // override methods
  ...

  // build the menu hierarchy
  public function build()
  {
    $this
    ->addChild('Home', '@homepage')->end()
    ->addChild('Contact', 'contact/form')->end()
    ->addChild('Blog', 'articles/list')->end()
    ->addChild('Sites')
      ->addChild('Diem', 'http://diem-project.org')->end()
      ->addChild('Symfony', 'http://symfony-project.org')->end()
    ->end();

    return $this;
  }
}
[/code]

Instantiate your menu in a component by specifying your custom class
[code php]
$this->menu = $this->getService('menu', 'myMenu');
[/code]

Then render the menu in a partial
[code php]
echo $menu;
[/code]

## Recursive menus
If you need to build automatic recursive menus, use the **->addRecursiveChildren($depth)** method. All descendant pages are included and nested in the menu item.
For example, if we have the following page structure:
[code]
main/root
  main/music
    music/rock
    music/jazz
  main/code
[/code]
You can write
[code php]
$menu
->addChild('Home', 'main/root')
->addRecursiveChildren(1);
[/code]
It will add main/root descendants with a depth of 1. It does the same as:
[code php]
$menu
->addChild('Home', 'main/root')
  ->addChild('Music', 'main/music')->end()
  ->addChild('Code', 'main/code');
[/code]
And with a depth of 2:
[code php]
$menu
->addChild('Home', 'main/root')
->addRecursiveChildren(2);
[/code]
I fetches main/root descendants with a depth of 2, and does the same as:
[code php]
$menu
->addChild('Home', 'main/root')
  ->addChild('Music', 'main/music')
    ->addChild('Rock', 'music/rock')->end()
    ->addChild('Jazz', 'music/jazz')->end()
  ->addChild('Code', 'main/code');
[/code]
##Move menu items
The methods *moveToFirst* and *moveToLast* allow to move a menu item at the first or last position of its siblings.
With the previous menu example,
[code php]
$menu['Home']['Music']->moveToLast();
[/code]
Will place Music before Code.
[code php]
$menu['Home']['Music']['Jazz']->moveToFirst();
[/code]
Will place Jazz before Rock.
## Built-in ready to use menus
### Sitemap
It is always good to have a sitemap page in your site for **SEO and accessibility** reasons.
The **sitemap_menu service** allows you to create one with very few work.

Add a main/sitemap action to your [modules.yml file](page:39), and refresh the site to create the component and partial.
Then in the component, instantiate the sitemap_menu:
[code php]
$this->menu = $this->getService('sitemap_menu')->build();
[/code]
And display it in the partial:
[code php]
echo $menu;
[/code]

## Change menu default class and options
As the menu service is managed by the [Service Container](page:46), you can easily change the default class and options for all your site menus:
_apps/front/config/dm/services.yml_
[code]
parameters:

  menu.class:    myCustomMenu // change class for all menus
  menu.options:
    translate:   false        // disable translation for all menus
    show_id:     true         // enable HTML id for all menus

  sitemap_menu.class: myCustomSitemapMenu // change class for sitemap menu
[/code]