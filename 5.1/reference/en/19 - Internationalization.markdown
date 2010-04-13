## Multilingual website

Available cultures can be declared in
**config/dm/config.yml**
~~~
all:

  i18n:
    cultures:  [ en, fr, de ]
~~~
The first culture is the default one.

### Culture switcher
Authenticated users can use the tool bar culture selector.
To allow non-authenticated users to switch to another culture, the dmCore/selectCulture action can be used.

#### Example: use links to switch culture
In a front template:
[code php]
echo _open('ul');

foreach(sfConfig::get('dm_i18n_cultures') as $culture)
{
  echo _tag('li',
    _link('+/dmFront/selectCulture')  // link to the dmFront/selectCulture symfony action
    ->param('culture', $culture)      // the culture parameter
    ->param('page_id', $dm_page->id)  // by passing the current page id, we get redirected to this page after the culture changed
    ->text($culture)                  // the link text
  );
}

echo _close('ul');
[/code]
*This template uses [Diem Template helpers](page:45)*.

The dmFront/selectCulture action will change the user culture, then redirect to the previous page.