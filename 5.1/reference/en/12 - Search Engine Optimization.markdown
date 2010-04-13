We think Search Engine Optimization is not the developer work.
Modify pages **metas** and **url** should require no technical knowledge.
The symfony native routing is great, but will you ask a non-developer to modify an YAML file ?

Diem provides a set of graphical tools that allow to easily control pages metas and url from the browser.

## Page metas and url

### Manual pages

When a user which belongs to the "seo" group is logged, a toolbar appears on front application.
By clicking on the "Edit page" button, this dialog appears:

![seo dialog](/uploads/screen/seo_dialog.png)

#### slug
The slug is the page url, without it's domain name. If you choose a slug that is already used, you will get an error message.

#### name
The name is the text which is used in internal links. In this example, the page is named "Sites powered by Diem". If a web designer creates a link to this page without writing the link text, "Sites powered by Diem" will be used.

[code php]
// <a href="/tour/sites-powered-by-diem">Sites powered by diem</a>
_link('tour/sites')
[/code]
See the [_link template helper](page:45#_link-create-a-link:internal-links)

#### title
The page title meta.
Will also be used as the link title attribute for internal links. In this example, the page has the title "Sites powered by Diem". If a web designer creates a link to this page without writing the title attribute, "Sites powered by Diem" will be used.

[code php]
// <a href="/tour/sites-powered-by-diem" title="Sites powered by diem">Showcase</a>
_link('tour/sites')->text('Showcase')
[/code]
See the [_link template helper](page:45#_link-create-a-link:internal-links)

You can disable these automatic link titles in the [configuration panel](page:44#graphical-configuration-panel), tab IHM.

#### h1
If you fill this field, the page's h1 will be replaced by your value.
If you let it empty, the web designer is responsible for the h1 content.

#### desc
The page description meta.

#### keywords
Everyone knows that web search engines ( like google ) don't care the keywords meta. But is this a valuable reason to omit them ?
As all over page metas, the keywords will be used by the Diem internal search engine when indexing pages.

### Automatic pages
Great websites reach hundreds of pages, and Diem is precisely made for that.
There is an efficient way to control pages attributes for a group of pages, instead of writing them one-by-one.

On the admin application, users that belong to the "seo" group can access to the automatic metas page:

![automatic page metas link](/uploads/screen/auto_page_link.png)

It leads on a interface that may seem tricky to use, but is incredibly useful.

![automatic page metas](/uploads/screen/auto_page_interface.png)

In this example, we have pages about "Documentation pages". The page you are reading is one of them.
We can describe all these pages attributes in one time, using simple patterns.
It's about placing variables ( like the doc name ) into the fields to generate them automatically.

### Variables
On the left of the screen, are the available variables. The "Documentation page" object has 4 variables available : %docPage.name%, %docPage.resume%, %docPage.text%, %docPage.id%.

But there is more. As "Documentation page" object is always linked to a "Documentation" object, we can also use "Documentation" variables. Click on the "Documentation" tab to see them.
The same way, as a "Documentation" object is linked to a "Branch", we can also access "Branch" variables.

Additionally, you can always use the %culture% variable.

### Metas fields
On the center of the screen are the fields we will fill to generate the pages attributes. These fields are the same ones as for <a href="#page-metas-and-url:manual-pages">manual pages</a>

But this time, we will put variables inside to automate the values creation.

In this example, the slug field contains %doc_page.name% ( can be written %docPage.name%, it's the same ). It means that the "Documentation page" page slugs will be the name of the "Documentation page" object that is showed.

Note that if you don't start the slug with a /, the parent page slug will be added, which is generally a good thing.

In this example, we can see the title meta is generated with two variables : the associated branch name, and the doc_page name.
You can also write static text in addition to variables.

All values are properly escaped, and the markdown code is transformed to text.

On the bottom is a button to preview the changes you made, and another one to apply them.

### Preview
On the right of the screen is a preview of what will be generated. Each time you click on the preview button, a random page of the current group will be chosen. Make sure to preview several times before applying changes.

## Url changes and broken links

By changing the urls again and again, we could expect that links will get broken. In fact, they don't.

### Smart links

At the same time you change the pages urls, the href attributes of all links are updated.

### Smart 404

As links from external websites cannot be automatically updated, there is a workaround.
By default, when a page is not found, Diem will redirect the user to *the most pertinent page*.
For example, try to go on this page ; it does not exists : http://diem-project.org/install
You will be automatically redirected to the installation page.

As a permanent redirection is used, search engine crawlers get informed that the page has moved.

The internal search engine is used to find the best page, so you should update it frequently.

If no matching page is found ( http://diem-project.org/guitar ) the main.error404 page is served.

This way, when you change a page url, there are chances that the old url will still match it, because the page is still relevant.

## SEO configuration

Options are available in the [configuration panel](page:44#graphical-configuration-panel), tab IHM & SEO.
As they are quite intuitive, and commented on the interface, we will not describe them here.

## Redirections
Sometimes we need to redirect an url to another one. Sometimes we make it with the .htacces file. It's a good solution, but it's not very user friendly, and a syntax error is disastrous.

So Diem provides a redirection interface to allow you to easily and safely create redirections:

![redirect interface](/uploads/screen/redirect.png)

When adding a redirection, we see to fields :

- **Old url** the bad url we want to redirect, without the domain name
- **New url** where we will be redirected.

### to a static url
You can set a static full url ( http://anothersite.com ) to the new url. But if the new url is an internal page of our site, it's far better to do :

### to a dynamic url
From the PAGES panel on the left, drag&drop a page on the **new url** field. This means that the old url is redirect to this page ; whatever its url is. This way, if the page we redirect to changes its url, the redirection will be updated too.

## Sitemap generator
Diem features a 100% automatic sitemap.xml generator. You will find it in the Seo -> Sitemap menu.

Just click on the "Generate sitemap" button, and a sitemap.xml is generated.

Note that the sitemap is not automatically updated when a page is added : you need to click on the button each time you want to refresh the sitemap.

## SEO services

### Google analytics
A user which belongs to the "seo" group can access to the google analytics page. On the admin upper tool bar, click "Seo" then "Google analytics".

An interface allows to configure the Ga key used to send reports, but also an email and password.
If you fill them, Diem will be able to get informations from google analytics, and display beautiful diagrams.

### Google webmaster tools
A user which belongs to the "seo" group can access to the google analytics page. On the admin upper tool bar, click "Seo" then "Google webmaster tools".

An interface allows to configure the Gwt key used to verify the site. Just fill the key, like "Kn5XiidGrARfD6Z_K5H90D6Ov9K2P27TaHOSCpJw1m0".
Diem will automatically add the google-site-verification meta on the homepage head.