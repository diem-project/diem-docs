Diem is designed to build high-performance websites. Performance is about reducing the page creation time, saving bandwidth and making pages display quickly on the user browser.
Diem provides several tools to make your website faster.

## Tweak the server
Here is an extract of [symfony documentation about performances](http://www.symfony-project.org/book/1_2/18-Performance#chapter_18_tweaking_the_server "").
*A well-optimized application should rely on a well-optimized server. You should know the basics of server performance to make sure there is no bottleneck outside symfony.*

### Use APC
The best way to improve server-side performances is to use APC. It stores PHP opcode in memory and dramatically reduces filesystem calls and compilation times.
Furthermore, when APC is installed, Diem will use it to cache efficiently your data: doctrine queries, template cache, routing and more.
*config/dm/config.yml*
[code]
all:
  cache:
    apc:                  true          # (RECOMMENDED) Use Apc if available
[/code]
If you set apc to false, or if APC is not available on your server, Diem will use filesystem cache instead.

#### Manage APC cache
Diem provides built-in tools to deal with APC. If APC is available on your server, and if you have superadmin permission, you can see an indicator on admin tool bar:
![](media:750)
By clicking on it you will access a full-featured, secured interface to manage APC cache.

#### Increase APC memory
Generally the default memory allocated to APC is unsufficient. If you see the APC indicator becoming full, Diem can no more store cache with APC and the performances are degraded. You should consider increasing APC memory size.
On an ubuntu server, it can be done in
*/etc/php5/apache2/php.ini*
[code]
extension=apc.so
apc.enabled=1
apc.shm_segments=1
apc.shm_size=256
[/code]
If you have many sites running on the same server, you could need to increase APC memory size again.
### Disable magic_quotes_gpc
As said in [symfony documentation about performances](http://www.symfony-project.org/book/1_2/18-Performance#chapter_18_tweaking_the_server ""):
*Having magic_quotes_gpc turned on in the php.ini slows down an application, because it tells PHP to escape all quotes in request parameters, but symfony will systematically unescape them afterwards, and the only consequence will be a loss of time--and quotes-escaping problems on some platforms. Therefore, turn this setting off if you have access to the PHP configuration.*

## Serve pages faster

### Cache front widgets
All front widgets can be cached separatly. It's a very good way to improve pages creation performance, and allows a fine, granular caching.
For example, if you have a "article" module with some components, you can write:
*config/dm/modules.yml*
[code]
    article:

      components:

        list:     { cache: true }   # cache the article/list widgets
                                    # the cache will be different for each page
                                    # where this widget appears

        listMenu: { cache: static } # cache the article/listMenu widget
                                    # the cache will be the same for each page
                                    # where this widget appears

        show:                       # don't cache the article/show widget
[/code]
By defaults, widgets are not cached. You need to specify manually which widgets are cached.
The recommended cache value is *true*. It makes the cache different on each page. Setting the cache to *static* makes the same cache used on every pages. It can reduce the cache size but may lead to problems if the widget relies on the page context.
If APC is available, it is used to store the widgets cache.

### Cache entire pages
A very efficient way to reduce page processing is of course to cache the entire page. To enable full page cache, turn it on in
*config/dm/config.yml*
[code]
  performance:
    page_cache:                         # Will cache entire pages and serve them very fast.
      enabled:            true          # Enable the page cache for all pages
      life_time:          86400         # Server-side lifetime of the cache in seconds (86400 seconds equals one day)
[/code]
Cached page are served immediatly, without any database query nor symfony action called.
Diem page cache uses symfony action cache internally, and caches the layout too. If APC is available, it is used to store the pages cache.
You can see pages served from the cache on admin request logs. A lightning icon appears when the page is cached:
![](media:748)
If you want to disable page cache for certain urls, use the [dm.page_cache.enable event](page:30#events-list:front-events:dm-page_cache-enable).

### Disable loading of Swift mailer
By default, symfony loads Swift mailer for each request. As most of the requests to your website don't trigger the sending of an email, it's generally a waste of time and memory. With Diem you can easily disable it:
*config/dm/config.yml*
[code]
  performance:
    enable_mailer:        false         # Set to false to disable Swift loading: significant performance boost.
                                        # If set to false, you can enable the mailer on demand with dm::enableMailer()
[/code]
If you set enable_mailer to false, the Swift mailer is not loaded and some time and memory is saved. If you need to send a mail, you can enable the mailer on demand:
[code php]
dm::enableMailer(); // loads Swift mailer on demand
$message = Swift_Message::newInstance();
$this->getMailer()->send($message);
[/code]

## Reduce number of HTTP requests

### Combine stylesheets and javascripts

Diem allows to combine and minify all your javascripts and stylesheets very easily:
*config/dm/config.yml*
[code]
  js:
    compress:             true

  css:
    compress:             true
[/code]
That's all. All your js and css files will be minified and combined to only one file. This file is cached for later requests directly in the web directory: this way, when the browsers asks for it, it does not require to run PHP. The assets are served as fast as possible.
Diem knows when to clear the assets cache. Just modify a stylesheet or javascript file and the asset cache will be regenerated automatically.
When you browse the site with enough permissions to use the code editors, your stylesheets and javascripts are NOT compressed to ease debugging with firebug.

## Reduce bandwidth
The previous tip already reduces a lot the bandwidth, because all stylesheets and javascripts are minified.
But you can go even farther.

### Gzip assets
You can enable **gzip compression** for javascript and stylesheet files. It reduces their weight by three to four times.
This optimisation requires **apache2**.

#### Enable gzip compression

Just change an option on Diem javascript and stylesheet compressors to make them produce gzipped files.
*config/dm/services.yml*
[code]
parameters:

  javascript_compressor.options:
    gz_compression: true

  stylesheet_compressor.options:
    gz_compression: true
[/code]

#### Forward assets requests
Now, we will tell apache to forward requests to a .css or .js to the gzipped file, if any.
*web/.htaccess*, uncomment the lines between
  \# SEND GZIPPED CONTENT TO COMPATIBLE BROWSERS
and
  # END GZIPPED CONTENT

You should now have:
[code]
  # SEND GZIPPED CONTENT TO COMPATIBLE BROWSERS
  RemoveType .gz
  RemoveOutputFilter .css .js
  AddEncoding x-gzip .gz
  AddType "text/css;charset=utf-8" .css
  AddType "text/javascript;charset=utf-8" .js
  RewriteCond %{HTTP:Accept-Encoding} gzip
  RewriteCond %{REQUEST_FILENAME}.gz -f
  RewriteRule ^(.*)$ $1.gz [L,QSA]
  # END GZIPPED CONTENT
[/code]

#### Try it
Open a page of your website and inspect requests with firebug. The server now sends gzipped assets, and load time is reduced.

This tip will not work on all servers, depending on apache configuration.

## Use profiling tools
Yahoo provides Yslow, an extension to firebug, to help you diagnosticate your pages performance problems.
This website, http://diem-project.org/, applies the previous recommandations and naturally gets the A-grade, the best performance notation ever according to Yahoo guidelines:
![](media:749)