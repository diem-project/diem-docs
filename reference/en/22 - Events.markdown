Diem uses the [Symfony Event Dispatcher](http://components.symfony-project.org/event-dispatcher/) intensively.
This allows services to be decoupled. This allows you to alter the way they work without extend them.

See the [symfony documentation about how to use events](http://www.symfony-project.org/blog/2009/02/21/using-the-symfony-event-system)

## Working with events

A good place to listen events is the application configuration class :

[code php]
require_once(dm::getDir().'/dmFrontPlugin/lib/config/dmFrontApplicationConfiguration.php');

class frontConfiguration extends dmFrontApplicationConfiguration
{
  public function configure()
  {
    // Register our listeners
    $this->dispatcher->connect('dm.refresh', array($this, 'listenToRefreshEvent'));
  }

  public function listenToRefreshEvent(sfEvent $e)
  {
    // do something when project has been successfully refreshed
  }
}
[/code]

## Events list

### Core events

Core events are notified in both admin and front applications.

#### dm.context.loaded
fired by **dmContext** when it's fully loaded with a ready to go serviceContainer
parameters: none

#### dm.context.end
fired by **dmContext** when application has been successfully executed
parameters: none

#### dm.browser.unknown
fired by **dmBrowser** when it is unable to configure itself from user agent because it is unknown
parameters: the user agent string

#### dm.i18n.not_found
fired by **dmI18n** when a translation is missing. The listener can assign a translation to the event's return value to handle the situation
parameters: array('string' => the untranslated string, 'args' => array, 'catalogue' => the current catalogue )

#### dm.record.modification
fired by **dmDoctrineRecord** when it will be saved or deleted
parameters: array('type' => create|update|delete)

#### dm.refresh
fired by **dmCoreActions** when user refreshed the site.
parameters: none

#### dm.cache.clear
fired by **dmCacheCleaner** when all cache has been cleared
parameters: array(success => wether the cache has been cleared successfully )

#### dm.cache.clear_template
fired by **dmCacheCleaner** when template cache has been cleared
parameters: none

#### dm.service_container.configuration
fired by **dmServiceContainerLoaderConfiguration** when the service container has been configured, before it is dumped to php
parameters: array(container => the service container, config => array key=>value config from dmConfig)

#### dm.data.before
fired by **dmDataLoad** before loading data
parameters: none

#### dm.data.after
fired by **dmDataLoad** after loading data
parameters: none

#### dm.setup.before
fired by **dmSetupTask** before updating the project
parameters: array(clear-db => wether the db will be cleared)

#### dm.setup.after
fired by **dmSetupTask** after updating the project
parameters: array(clear-db => wether the db has been cleared)

#### dm.page.post_save
fired by **DmPage** when a page record has been saved.
parameters: none

#### dm.service_container.pre_dump
fired by **dmContext** when dumping a new service container. With this event you can modify the service container just before it's dumped to PHP.
parameters: none
value: sfServiceContainerBuilder the service container builder ready to be dumped

#### dm.javascript_compressor.minifiable
fired by **dmJavascriptCompressor** before minifying a javascript file. With this event you can enable/disable the minification by returning true or false.
parameters: array( path => the javascript path )
value: minifiable whether the asset is minifiable or not

#### dm.javascript_compressor.cachable
fired by **dmJavascriptCompressor** before caching a javascript file. With this event you can enable/disable the caching by returning true or false.
parameters: array( path => the javascript path, options => the javascript options )
value: cachable whether the asset is cachable or not

####dm.search.filter_boost_values
fired by **dmSearchPageDocument** just before indexing a page. With this event you can filter and modify the field boost values.
parameters: array( page => the current page we are indexing )
value: array( field_name => boost_value )
For example, if we want to make the page body more important for all article/show pages:
*apps/front/config/frontConfiguration.class.php*
[code php]

class frontConfiguration extends dmFrontApplicationConfiguration
{

  public function configure()
  {
    // connect to the dm.search.filter_boost_values event
    $this->getEventDispatcher()->connect(
      'dm.search.filter_boost_values',
      array($this, 'listenToSearchFilterBoostValues')
    );
  }

  // listen to the dm.search.filter_boost_values event
  public function listenToSearchFilterBoostValues(sfEvent $event, array $boostValues)
  {
    if($event['page']->isModuleAction('article', 'show'))
    {
      $boostValues['body'] = $boostValues['body'] * 2;
    }

    return $boostValues;
  }
[/code]
You can throw a dmSearchPageNotIndexableException to exclude the page from the search index.
[code php]
  public function listenToSearchFilterBoostValues(sfEvent $event, array $boostValues)
  {
    if($event['page']->isModuleAction('main', 'sitemap'))
    {
      throw new dmSearchPageNotIndexableException();
    }

    return $boostValues;
  }
[/code]

####dm.table.filter_seo_columns
fired by **dmDoctrineTable** just before returing seo columns. With this event you can filter and modify the seo columns of a table. Seo columns appear in automatic seo interface.
parameters: none
value: array( seo_column )

####dm.mail.pre_send
fired by **dmMail** just before sending a mail.
parameters: array('mailer' => sfMailer, 'message' => Swift_Message, 'template' => DmMailTemplate)

####dm.mail.post_send
fired by **dmMail** just after sending a mail.
parameters: array('mailer' => sfMailer, 'message' => Swift_Message, 'template' => DmMailTemplate)

### Admin events

Admin events are only notified in admin application.

####dm.admin_homepage.filter_windows
fired by **dmAdminHomepageManager** just before displaying the windows. With this event you can filter and modify the windows.
[code php]
$this->dispatcher->connect('dm.admin_homepage.filter_windows', array($this, 'listenToFilterWindowsEvent'));

//---------------------------------------------------------------------

public function listenToFilterWindowsEvent(sfEvent $event, array $windows)
{
  // add a myWindow in second column
  $windows[1]['myWindow'] = array($this, 'renderMyWindow');

  // change the existing weekChart renderer
  $windows[0]['weekChart'] = array($this, 'renderWeekChart');

  return $windows;
}

// ---------------------------------------------------------------------

public function renderMyWindow(dmHelper $helper)
{
  // render the window with the $helper
}
[/code]

#### dm.admin.menu
fired by **dmAdminMenu** when it has been initialized. With this event you can modify the admin menu.
parameters: none

#### dm.bread_crumb.filter_links
fired by **dmAdminBreadcrumb** before rendering links. With this event you can modify breadCrumb links just before rendering.
parameters: none
value: array of linkType => options

#### dm.search.populated
fired by **dmSearchIndex** when index has been written to disk
parameters: array(culture => index culture, name => index name, nb_documents => number of documents generated, time => population time in seconds)

#### dm.sitemap.generated
fired by **dmSitemap** when sitemap has been written to disk
parameters: array(file => full server path, url => full web path)

#### dm.config.updated
fired by **static** dmConfig when a value has been changed
parameters: array(setting => DmSetting record, culture => culture value)

#### admin.edit_object
fired by an admin module edit action when a record will be edited or updated
parameters: array(object => dmDoctrineRecord instance)

#### dm.media_library.control_menu
fired by dmMediaLibraryControlMenu when it's built. With this event you can change the menu just before it is rendered.
parameters: array(folder => DmMediaFolder the current folder)

#### dm.content_chart.filter_modules
fired by dmContentChart before building the chart. With this event you can change the modules shown in the chart.
parameters: none
value: array of module names

### Front events

Front events are only notified in front application.

#### dm.layout.filter_stylesheets
fired by **dmCoreLayoutHelper** before rendering stylesheets. With this event you can modify stylesheets just before rendering.
parameters: none
value: array of stylesheets => options

#### dm.layout.filter_javascripts
fired by **dmCoreLayoutHelper** before rendering javascripts. With this event you can modify javascripts just before rendering.
parameters: none
value: array of javascripts => options

#### dm.layout.filter_metas
fired by **dmCoreLayoutHelper** before rendering metas. With this event you can modify metas just before rendering.
parameters: none
value: array of name => value

#### dm.theme.created
fired by **dmTheme** when it has finished creating its filesystem structure
parameters: none

#### dm.context.change_page
fired by **dmFrontContext** when current page has changed
parameters: array('page' => DmPage record)

#### dm.bread_crumb.filter_links
fired by **dmWidgetNavigationBreadcrumb** before rendering links. With this event you can modify breadCrumb links.
parameters: array( page => DmPage record, the current page )
value: array of module.action => dmLinkTag

#### dm.bread_crumb.filter_pages
fired by **dmWidgetNavigationBreadcrumb** before rendering page links. With this event you can modify breadCrumb pages.
parameters: array( page => DmPage record, the current page )
value: array of module.action => DmPage record

#### dm.page_not_found.before
fired by **dmPageNotFoundHandler** with a notifyUntil before searching for a possible redirection.
Assign an url to the event's return value to redirect the request.
parameters: array(slug => the not found slug)

#### dm.page_not_found.after
fired by **dmPageNotFoundHandler** with a notifyUntil after searching for a possible redirection, and found nothing.
Assign an url to the event's return value to redirect the request.
parameters: array(slug => the not found slug)

#### dm.front.add_menu
fired by **dmFrontAddMenu** when it has been initialized. With this event you can modify the front add menu.
parameters: none

#### dm.page_cache.enable
fired by **dmFrontInitFilter** to decide whether or not the current page should be cached. This event is notified when a page is about to be cached or served from the cache. You can return false to skip the cache and render the page normally. At this step of the execution, you don't have access to the DmPage record, so you must use the context and the request uri to decide if you allow the page cache or not.
parameters: array(context => the symfony context)
value: (boolean) true if the current page can be cached, false otherwise.

#### dm.page.deny_access
fired by **dmFrontActions** to decide whether or not the access to the current page is denied. This event is fired before processing any page, with the value *true* if access is denied, and "false" if access is granted. If you listen this event, return *true* to deny access or *false* to grant it. If access is denied, the request is forwarded to the main/signin page.
parameters: array(page => the current page, context => the current symfony context)
value: (boolean) true if access is denied, false otherwise.