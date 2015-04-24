# Understanding the Country Module
The goal is to learn how to create modules by reading/understanding
a few of them.

## Module chosen because:

- It doesn't seem too complicated
- It has few dependencies on other modules.
- It is an official module.
- It does not require business knowledge. Everybody knows about countries.


## Where to find original code:

- [official repo in hg.tryton.org](http://hg.tryton.org/modules/country)
- [mirror in github](https://github.com/tryton/country)

## Configuration file

### tryton.cfg

    [tryton]
    version=3.4.2
    depends:
        ir
        res
    xml:
        country.xml

- version: it seems to match the current version of tryton, not too sure if
that's what you are suppose to do, match the version of tryton that the module is
suppose to work with/for.
- depends: names of other modules this module depends on.
- xml: list of xml files for this module.

### __init.py__

    from trytond.pool import Pool
    from .country import *

    def register():
        Pool.register(
            Country,
            Subdivision,
            Zip,
            module='country', type_='model')

- register the module 'country' with the Models Country, Subdivision and Zip.

### country.py

    from trytond.model import ModelView, ModelSQL, fields
    from trytond.pyson import Eval

    __all__ = ['Country', 'Subdivision', 'Zip']

- \_\_all\_\_ seems to be required, it contains all the Models.

#### Country

    class Country(ModelSQL, ModelView):
        'Country'
        __name__ = 'country.country'

- Model Country is persisted (ModelSQL) and we have a view/form (ModelView).
- It's name is country.country (Module country, model country)

        name = fields.Char('Name', required=True, translate=True,
               help='The full name of the country.', select=True)
        code = fields.Char('Code', size=2, select=True,
               help='The ISO country code in two chars.\n'
              'You can use this field for quick search.', required=True)
        subdivisions = fields.One2Many('country.subdivision',
              'country', 'Subdivisions')

- The model has 3 fields name, code, subdivision.
- Field definitions [Field api](http://doc.tryton.org/3.6/trytond/doc/ref/models/fields.html#ref-models-fields)
- select = True -> indexed.
- translate = True -> translatable, value depends on language context.
- note: no constraints yet. They will be defined next in a classmethod.

        @classmethod
        def __setup__(cls):
           super(Country, cls).__setup__()
           cls._sql_constraints += [
               ('name_uniq', 'UNIQUE(name)',
                   'The name of the country must be unique.'),
               ('code_uniq', 'UNIQUE(code)',
                   'The code of the country must be unique.'),
                ]
           cls._order.insert(0, ('name', 'ASC'))

- override [ModelSQL./_/_setup/_/_](http://hg.tryton.org/trytond/file/10cfbb9153b6/trytond/model/modelsql.py)
- adds constraints unique name and code.
- the "default" order will by first by 'name' and then 'id' (surrogate primary key added to all Models).
