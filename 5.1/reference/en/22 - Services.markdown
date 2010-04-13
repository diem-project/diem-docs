This is one of the greatest Diem features.
Every part of Diem is called a service. And you can replace and extend each of them.

Learn about the [Symfony Dependency Injection Container](http://components.symfony-project.org/dependency-injection/)

##Replace a Diem service

Let's suppose we want to change the way Diem detects browsers. This is done with the *browser_detection* service. We will tell Diem to use another class for the browser detection service in
**config/dm/services.yml**
~~~
parameters:

  browser_detection.class: myBrowserDetection
~~~

Then we create the myBrowserDetection class in
**lib/myBrowserDetection.php**
[code php]
<?php
class myBrowserDetection extends dmBrowserDetection
{
  public function execute($userAgent)
  {
    // your own way to detect browser based on USER_AGENT
  }
}
[/code]

## Core

The core services can be used in both admin and front application.
To extend them, use the config/dm/services.yml file.
The original services.yml can be found in dmCorePlugin/config/dm/services.yml.

###markdown
Responsible for parsing [markdown text](http://daringfireball.net/projects/markdown/syntax) and generating HTML.

- **auto_header_id**(bool) whether to generate automatic header ids, based on header text and previous headers structure.

###file backup
Responsible for saving a copy of files edited. All files modified with a Diem code editor are backuped.

- **dir**(string) where to save files, relative to project root directory. Defaults to *data/dm/backup/filesystem*

###text_diff
Responsible for generating visual text diffs

###record_text_diff
Responsible for rendering diffs between two versions of a record

###mail
Responsible for building an email from a DmMailTemplate and a data array, then pass it to Swift.
**This service is not yet ready for usage**

###thread_launcher
Responsible for launching background tasks using an internal shell.

- **cli_file**(string) where to write the task bootstrap, relative to the project root directory

###page_synchronizer
Responsible for adding, updating and removing pages when the site content changes.

###seo_synchronizer
Responsible for updating pages metas when the site content changes.

###cache_cleaner
Responsible for clearing the cache automatically when the site content changes.

- **applications**(array) applications whose cache should be cleared
- **environments**(array) environments whose cache should be cleared
- **safe_models**(array)  when a instance of this models is created/updated/deleted, the cache is **NOT** cleared.

###form_field
Represents a field in a form. Allows chainability in [form template helpers](page:45#form-helpers:form-helpers:display-a-form-widget).

###script_name_resolver
Responsible for finding applications bootstrap urls. Allows to make a link to the site from the admin, and a link to admin from the site.

###error_watcher
Responsible for listening the "application.throw_exception" events and make something with the errors.

- **error_description_class**(string) class used to describe an error
- **mail_superadmin**(bool) send mail to superadmin ( uses superadmin's email )
- **store_in_db**(bool) store error in database

###media_tag_image
Responsible for rendering *img* tags with the [_media helper](page:45#_media-create-a-media). Configurable from the [graphical configuration panel](page:44#graphical-configuration-panel).

###media_tag_flash
Responsible for rendering flash objects with the [_media helper](page:45#_media-create-a-media). Not ready yet, you should extend it if you need it.

###media_tag_video
Responsible for rendering video with the [_media helper](page:45#_media-create-a-media). Not ready yet, you should extend it if you need it.

###media_tag_audio
Responsible for rendering audio with the [_media helper](page:45#_media-create-a-media). Not ready yet, you should extend it if you need it.

###media_resource
Ressource for a media_tag_*

###table_tag
Responsible for rendering HTML tables with the [_table helper](page:45#_table-create-a-table)

###search_engine
Responsible for managing search indices and provide a handfull programming interface.

- **dir**(string) where to save the indices, relative to project root directory

###search_index
Responsible for maintaining and querying an index ( for example, the "en" index )

- **dir**(string) where to save the index, relative to project root directory
- **culture**(string) user will ben given this culture during index population
- **name**(string) index name

###search_document
Responsible for indexing a page

- **culture**(string) user will ben given this culture during document population

###search_hit
Single search result

- **score**(float) hit score in % ( will be set by search_index when creating the hit )
- **page_id**(int) hit page id ( will be set by search_index when creating the hit )

###theme
Responsible for managing a CSS theme

###stylesheet_compressor
Responsible for minifying, merging and compressing stylesheets

- **minify**(bool) whether to minify code
- **gz_compression**(bool) whether to compress with gzip

###javascript_compressor
Responsible for minifying, merging and compressing javascripts

- **minify**(bool) whether to minify code
- **black_list**(array) List of filenames which must not be minified
- **gz_compression**(bool) whether to compress with gzip

###auth_layout_helper
Responsible for rendering the authentication page layout

###cache_manager
Responsible for managing cache

- **meta_cache_class**(string) class for metacache ( use file cache or apc cache depending on server )

###filesystem
Extension of sfFilesystem

###event_log
Responsible for logging all notable events

- **name**(string) the log visible name
- **file**(string) where to save the log file, relative to project root directory
- **entry_service_name**(string) service name for an entry of this log
- **rotation**(bool) enable rotation on the log file ( strongly recommended )
- **max_size_megabytes**(float) max size for the log file before rotating
- **ignore_models**(array) models not to log
- **ignore_internal_actions**(bool) whether to ignore Diem internal actions

###event_log_entry
An entry of the event log

###request_log
Responsible for logging all requests

- **name**(string) the log visible name
- **file**(string) where to save the log file, relative to project root directory
- **entry_service_name**(string) service name for an entry of this log
- **rotation**(bool) enable rotation on the log file ( strongly recommended )
- **max_size_megabytes**(float) max size for the log file before rotating

###request_log_entry
An entry of the request log

###browser
Represents a user browser

###browser_detection
Responsible for detecting user browser depending on its user agent

###page_tree_watcher
Responsible for listening all events that may require a page synchronization, and launch the synchronization if any before redirections.

- **use_thread**( auto, true, false ) whether to launch heavy synchronization tasks on another process

###helper
Object oriented template helper.

- **use_beaf**(bool) whether to use the "before-after" functionality on template helpers

###widget_type_manager
Responsible for managing widget types.

###page_i18n_builder
Multilingual sites only. When a page is created in a culture, this service will generate missing page translations for other cultures.

- **activate_new_translations**(bool) if set to true, the created translations are active and can be accessed by users. Defaults to false

###mime_type_resolver
Detects mime types from file names.

###project_loremizer
Fills the project with random records.

- **nb_records_per_table**(int) how many records to create for each table

###table_loremizer
Fills a table with random records.

- **nb_records**(int) how many records to create for this table
- **create_associations**(bool) create association records

###record_loremizer
Fills the record fields with random values.

- **override**(bool) replace existing values
- **create_associations**(bool) create association records

###doctrine_pager
Responsible for paginating doctrine collections

###web browser
Basic HTTP client. Overrides sfWebBrowser.

###data_load
Responsible for loading default data in the project

###tool_bar_view
Responsible for rendering admin & front toolbar. Implemented by dmAdminToolBarView on admin app, and by dmFrontToolBarView on front app. Use your apps/admin/config/dm/services.yml or apps/front/config/dm/services.yml file to change the class and modify the tool bar.

##Admin
The admin services can only be used in admin application.
To extend them, use the apps/admin/config/dm/services.yml file.
The original services.yml can be found in dmAdminPlugin/config/dm/services.yml.

###homepage_manager
Responsible for managing and rendering windows on admin homepage.
See the related [dm.admin_homepage.filter_windows event](page:30#events-list:admin-events:dm-admin_homepage-filter_windows).

###bread_crumb
Responsible for building and rendering the admin automatic breadcrumbs.

###log_chart
Responsible for rendering the log chart.

- **name**(string) public name for this chart
- **lifetime**(int) time in seconds to keep a cached version of the chart

###week_chart
Responsible for rendering the week chart.

- **name**(string) public name for this chart
- **lifetime**(int) time in seconds to keep a cached version of the chart

###visit_chart
Responsible for rendering the visit chart.

- **name**(string) public name for this chart
- **lifetime**(int) time in seconds to keep a cached version of the chart

###content_chart
Responsible for rendering the content chart.

- **name**(string) public name for this chart
- **lifetime**(int) time in seconds to keep a cached version of the chart

###browser_chart
Responsible for rendering the browser chart.

- **name**(string) public name for this chart
- **lifetime**(int) time in seconds to keep a cached version of the chart

###gapi
Responsible for fetching data from google analytics.

###link_tag
Responsible for rendering admin link tags with the [_link helper](page:45#link-create-a-link).

###asset_config
Responsible for loading required assets.

###admin_sort_table_form
Generic form to sort a table.

###admin_sort_referers_form
Generic form to sort table's referers.

###layout_helper
Responsible for rendering the admin layout.

###admin_menu
Responsible for building and rendering the admin menu.

###xml_sitemap_generator
Responsible for generating and saving automatic sitemap.xml files.

###related_records_view
Shows a list of records related to another one.

- *new*(bool) wether to add a link to create a new related record
- *sort*(bool) wether to add a link to sort related records
- *max*(int) max number of records to show

###log_view
Responsible for rendering a dmLog instance.

##Front
The front services can only be used in front application.
To extend them, use the apps/front/config/dm/services.yml file.
The original services.yml can be found in dmFrontPlugin/config/dm/services.yml.

###html_sitemap
**deprecated**: Please use **sitemap_menu** instead.
Responsible for building and rendering an HTML sitemap. Not to be confused with the xml sitemap, the HTML one is intended to be displayed on the site.

###sitemap_menu
Responsible for building and rendering an HTML sitemap. Not to be confused with the xml sitemap, the HTML one is intended to be displayed on the site.

###page_not_found_handler
Responsible to redirect or forward the user when the request page is not found.

###form_manager
Responsible for creating and managing front forms.

###link_tag_record
Responsible for rendering a link tag to a record with the [_link helper](page:45#_link-create-a-link). Configurable from the [graphical configuration panel](page:44#graphical-configuration-panel).

###link_tag_page
Responsible for rendering a link tag to a DmPage instance with the [_link helper](page:45#_link-create-a-link). Configurable from the [graphical configuration panel](page:44#graphical-configuration-panel).

###link_tag_media
Responsible for rendering a link tag to a DmMedia instance with the [_link helper](page:45#_link-create-a-link). Configurable from the [graphical configuration panel](page:44#graphical-configuration-panel).

###link_tag_action
Responsible for rendering a link tag to a symfony action with the [_link helper](page:45#_link-create-a-link). Configurable from the [graphical configuration panel](page:44#graphical-configuration-panel).

###link_tag_uri
Responsible for rendering a link tag to an external uri with the [_link helper](page:45#_link-create-a-link). Configurable from the [graphical configuration panel](page:44#graphical-configuration-panel).

###link_tag_error
Responsible for rendering a link tag to an exception with the [_link helper](page:45#_link-create-a-link). Configurable from the [graphical configuration panel](page:44#graphical-configuration-panel).

###link_resource
Ressource for a link_tag_*.

###asset_config
Responsible for loading required assets.

###theme_manager
Responsible for managing available themes.

###widget_view
Responsible for rendering a widget.

###layout_helper
Responsible for rendering the front layout.

###page_helper
Responsible for rendering the front content. Displays Areas, Zones and Widgets. This service has two implementations : page_helper.view_class and page_helper.edit_class. The implementation to use it chosen at runtime depending on the user permissions.

- **widget_css_class_pattern**(string) widget css class generation pattern.
Available variables : %module%, %action%.
Can be let empty to disable automatic widget classes.
Defaults to '%module%_%action%'

###widget_renderer
Responsible for rendering widgets.

- **config_file**(string) path to the widget type configuration file, relative to the project directory

###zone_form
Form used when displaying a zone edition dialog

###page_routing
Responsible for building a page_route from a slug

###page_route
Wraps a page and a culture that match a slug

###front_add_menu
Responsible for building and rendering the front add menu

###front_code_editor_file_menu
Responsible for building and rendering the front code editor file menu

###front_pager_view
Responsible for rendering a sfPager with pagination links