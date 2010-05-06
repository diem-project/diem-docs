Diem uses an object oriented layout and templating system.

##Page structure
In Diem, each page is made with a layout and a content.
Each page has its own content, but many pages can share the same layout.

###Layouts
A Diem layout is an object in the database.
It's designed to wrap a page content with 4 **Areas** : top, bottom, left and right.

![page structure](/uploads/screen/page_structure.png)

For example, this site uses the following layouts:

- A "Documentation" layout with a documentation menu in the right area
- A "Home" layout with nothing in the left and right areas
- A "Blog" layout with the posts lists in the right area

>**Add a new layout**
>You can manage layouts in the admin application, in Tools -> Layouts.
>When creating a new layout, you can decide to duplicate an existing one.
>You can add a CSS class to a layout. The CSS class is applied to the *body* tag.
>You can also choose a different template for each layout.

The page content, and each layout **Area**, contain **Zones**.

###Zones

They allow you to make columns into an area.

This is for example the content **Area** with **Zones** inside :

![area structure](/uploads/screen/area_structure.png)

>**Add a new zone**
>To add a zone on your site, click the "Add" button on front application.
>Then, drag&drop the "Zone" box to a layout Area, or to the content.
>A dialog appears where you can give a size to the zone ( like 50% or 300px )
>You can also add a Css class to the zone.

For example, if you need a page with 4 columns :

- one **Zone** 100% in the left **Area**
- two **Zones** 50% in the content **Area**
- one **Zone** 100% in the right **Area**

Each **Zone** contains **Widgets**.

###Widgets
A **Widget** is an independant piece of HTML like a breadcrumb, a blog post list, a blog post detail...
You can stack many **Widgets** vertically into a **Zone**.

This is for example the content **Area** with **Zones** and **Widgets** :

![zone structure](/uploads/screen/zone_structure.png)

##Advanced layout customisation

If you want to change the way **Areas** are displayed, copy and paste

    dmFrontPlugin/modules/dmFront/templates/pageSuccess.php

to

    apps/front/modules/dmFront/templates/pagesSuccess.php

Here is the default pageSuccess.php:
[code php]
<?php

// open the page div
echo _open('div#dm_page'.($sf_user->getIsEditMode() ? '.edit' : ''));

echo $helper->renderAccessLinks();             // render accessibility links

  echo _tag('div.dm_layout',                      // open the layout div

    $helper->renderArea('top', '.clearfix').   // render TOP Area

    _tag('div.dm_layout_center.clearfix',         // open the layout_center div

      $helper->renderArea('left').             // render LEFT Area

      $helper->renderArea('content').          // render page content Area

      $helper->renderArea('right')             // render right Area

    ).                                         // close the layout_center div

    $helper->renderArea('bottom', '.clearfix') // render the BOTTOM Area

  );                                           // close the layout div

echo _close('div');                                // close the page div
[/code]
*This file uses [Diem template helpers](page:45)*

This file applies on all pages and all layouts. It gives full control on html flow and allows to insert non-Diem stuff.

###Areas customization

Areas specified in the above example are the default ones, but you can create as many areas you want, just use unique name for them. Additionally you can specify areas binding to the layout or pages by using prefix along with area's name:
[code php]
echo $helper->renderArea('layout.content');
echo $helper->renderArea('page.right');
[/code]

On this example above the content area will be the same for all pages using our layout, and right area will be different for pages using this layout.

##HTML map

In this map we can see how each front page is built.

[code php]
<html>

  <head>
    ...                // managed by the layout_helper service
  </head>

  <body class="...">   // class configurable in Admin -> Tools -> Layouts

    <div id="dm_page">
      ...              // pageSuccess.php, template configurable in  Admin -> Tools -> Layouts
    </div>

    ...                // Diem edition stuff :
    ...                // tool bar, media bar, page bar, dialogs...

  </body>

</html>
[/code]
The header is managed by the [layout_helper](#override-the-layout_helper-service) service, that you can modify.

The body class is configurable with the Layout admin interface.

All page content is written into div#dm_page by [pageSuccess.php](#advanced-layout-customisation), that you can modify.

## Doctype

Diem defaults to HTML5 and uses new elements like *header* and *aside*.

If you want to use another doctype, open the apps/front/config/dm/config.yml file :
~~~
all:
  html:
    doctype:
      name:        html           # Doctype name ( 'html', 'xhtml' )
      version:     5              # Doctype xhtml version ( '1.0', '1.1' ) or html version ( '4', '5' )
      compliance:  transitional   # Doctype xhtml compliance ( 'strict', 'transitional' )
~~~

Diem will generate a valid doctype declaration and write compliant HTML code depending on the doctype you chose.

Additionally, when serving HTML5 pages to a browser that doesn't support it, [html5shiv](http://html5shiv.googlecode.com) is loaded. It allows the browser to recognise the HTML5 elements.

##Include stylesheets and javascripts
Like symfony, Diem uses the [apps/front/config/view.yml](http://www.symfony-project.org/reference/1_4/en/13-View#chapter_13_stylesheets) to include assets.
But with Diem, assets are merged into one file, compressed and sent to the browser with gzinp encoding.
When a user is authenticated with enough permissions to edit CSS files, the user stylesheets are not compressed. It makes easier to inspect the page with firebug.

##Include a favicon
Just add a **favicon.ico**, **favicon.png** or **favicon.gif** file in the web dir.
It will be included automatically.

If you want the favicon to be elsewhere, you can override the layout_helper service:

##Override the layout_helper service

*html*, *head* and *body* tags are managed by the layout_helper
service.
It takes care of specifying page culture, add seo metas,
include assets and all that kind of things we don't want to worry
about.
You may replace the layout_helper service class in your
[apps/front/config/dm/services.yml](page:46) if you want to change the way it works:
apps/front/config/dm/services.yml:
~~~
parameters:

  layout_helper.class:    myFrontLayoutHelper
~~~

Then create the file:
apps/front/lib/myFrontLayoutHelper.php
[code php]
<?php

class myFrontLayoutHelper extends dmFrontLayoutHelper
{
  // override methods you want to change
}
[/code]

Learn more about [Diem services](page:46).

##Template helpers
Learn more about [Diem template helpers on their dedicated chapter](page:45).