A Diem project is made of modules.

## Modules explanation by the example

Let's take the simple example of a webstore which contains products on categories.
Each category has a page containing the category name, and the list of products associated to this category.
Each product page contains the product description.

## Example data model
The [schema.yml](page:44#configuration-files:config-doctrine-schema-yml) file looks like this:

    Category:
      columns:
        name:             { type: string(255), notnull: true }
        body:             { type: clob, extra: markdown }

    Product:
      columns:
        category_id:      { type: integer, notnull: true }
        name:             { type: string(255), notnull: true }
        body:             { type: clob, extra: markdown }
      relations:
        Category:
          foreignAlias:   Products

## Modules declaration
Modules are declared in config/dm/modules.yml

    Content:

      Store:

        category:
          model:                  Category
          page:                   true
          name:                   Category|Categories
          components:
            list:
            show:

        product:
          parent:                 category
          page:                   true
          name:                   Product
          components:
            listByCategory:
              filters:            [ category ]
            show:

Note that the modules start with an underscore letter, and that words are separated by an uppercase letter. This is a mandatory [convention](page:38) name "[modulized](page:38#syntaxes:modulized)".

With these two files, Diem is able to build a fully functional project.

## The category module
This is the same modules.yml file, commented:

    Content:                  # declare modules for the project

      Store:                  # namespace, mainly used to structure the admin menu
                              # this has no meaning for Diem and follows no convention
                              # so we can use any name we want to

        category:             # declare the category module

          model: Category     # declare that the category module uses the Category model
                              # in this case this is not required,
                              # because module and model have the same name

          page: true          # declare that each Category record
                              # has a dedicated page on the front application

          name: Category|Categories # human name shown in admin and front menus.
                                    # As the plural is special for this word,
                                    # we specify it after a |

          components:         # declare the module components

            list:             # the name starts with the "list" keyword
                              # so Diem assumes it is used
                              # to display a list of Category records

            show:             # the name starts with the "show" keyword
                              # so Diem assumes it is used
                              # to display a single Category record
The category module:

- is linked to the Category model. An administration interface will be generated to manipulate the Category table.
- has a page. Each time a Category record is created, a page is added to display it.
- has two components: list and show. The components and partials will be created in the front application, with sensible default contents.

## The product module

        product:               # declare the product module
                               # in the same "Store" namespace

          page:        true    # declare that each Product record
                               # has a dedicated page on the front app

          parent:      category # declare that a product is child of a category

          components:          # declare the module components

            listByCategory:    # the name starts with the "list" keyword
                               # so Diem assumes it is used
                               # to display a list of Product records

              filters: [ category ] # declare that this list is filtered by a category:
                                    # it will display all the Product records
                                    # for a given Category Record

            show:              # the name starts with the "show" keyword
                               # so Diem assumes it is used
                               # to display a single Product record

The product module:

- is implicitly linked to the Product model. We didn't declare it but they have the same name
- has a page. Each time a Product record is created, a page is added to display it
- has a parent module: category. Each product page will be inserted as a child of the matching doc page
- has two components: listByCategory and show. The components and partials will be created in the front application

## Performance

The modules declaration is compiled, dumped to php, and stored in the cache directory.
You can see the dumped php code in cache/front/dev/config/config_dm_modules.yml.php

## Full example

Here is an example that uses most possibilities:


    product:                                    # module key

      parent:                 category          # module parent's key

      model:                  Category          # linked to the Category model

      page:                   true              # each Product record has a page in front app

      name:                   Product|Products  # singular name|plural name

      admin:                  true              # visible in admin app
      front:                  true              # visible in front app

      components:

        listByCategory:                         # list of products for one category

          filters:            [ category ]      # front list filters

          cache:              true              # the _listByCategory.php template is cachable
                                                # but varies on each page ( recommended )
        show:                                   # show one product

          cache:              static            # the _show.php template is cachable,
                                                # and never varies
        showLittle:                             # also show one product, with another component and template

          cache:              false             # the _showLittle.php template is never cached

##Specific widget creation
Each time you declare a component in the modules.yml file, a specific widget is created for your project.
A component and a partial are created in the front module dir, and the widget appears in the front tool bar "Add" menu, in order to allow you to drag&drop it somewhere on a page.

Learn more about [List widgets](page:117).