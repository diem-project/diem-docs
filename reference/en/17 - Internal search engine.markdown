## Use the internal search engine

### Display a search form

Just drag&drop a search/form widget from the front Add menu to a page.

### Display search results

Go to the "Search results" page (it has been created the same time as the search form). Drag&drop a search/results widget on it, from the front Add menu.

### Update the search index

The search index does **not** update automatically for performance reasons. So you need to refresh it manually when you change things on your site.
In admin, open Tools->Search Engine->Manage index. Click the "Reload index" button on the right to refresh the search index.

### Automate the search index creation
You should consider using cron or an equivalent to automate the search index update. See the admin Tools->Search Engine->Manage page for instructions about it.

### Smart redirections
The internal search index can be used to redirect non-existing urls to the better pages for these urls. Let's say Diem receives a request to **/contact-us**, but this page does not exist. Diem will query the internal search engine with the terms **contact** and **us**, and find the existing **/contact** page. So it will redirect the user to **/contact**. When no relevant page is found, the users gets the normal 404 error page.
This feature is enabled by default, you can disable it in the admin configuration panel, SEO tab.

## Tweak the indexation process

### How does Lucene Search indexes pages
Each page is a document in the search index. A document contains several fields like "name", "slug", "description" or "body".
Each field has a boost value to define how much it's relevant. For example, by default, "body" has a boost value of 1 and "name" has a boost value of 3. This means the page name is by three times more important than the page body.

### Change boost values
#### For all pages
The easiest way to change fields boost values is to configure the **search_document** service, using the Dependency Injection Container configuration file.
Let's suppose you want to reduce the importance of page body, and increase importance of page name.
*config/dm/services.yml*
[code]
parameters:

  search_document.options:
    boost_values:
      body:       0.5
      name:       5
[/code]
You need to clear the cache after editing the services.yml file.

#### Depending on the page
If you need to make the boost values depend on the page beeing indexed, you can use the **dm.search.filter_boost_values** event to filter them.
Please refer to the [dm.search.filter_boost_values event documentation](page:30#events-list:core-events:dm-search-filter_boost_values) to see how to do that.

### Override the search_document service
If you need more specific indexation rules, you can override the service responsible for indexing a page.

#### Declare our own search_document implementation class
*config/dm/config.yml*
[code]
parameters:

  search_document.class:      mySearchPageDocument
[/code]
You need to clear the cache after editing the services.yml file.
#### Create the mySearchPageDocument class
*lib/mySearchPageDocument.php*
[code php]
class mySearchPageDocument extends dmSearchPageDocument
{
  // here, override the methods
}
[/code]