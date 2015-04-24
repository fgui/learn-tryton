# Understanding the Country Module
The goal is to learn how to create modules by reading/understanding
a few of them.

## Module chosen because:

- It doesn't seem too complicated
- It has few dependencies on other modules.
- It is an "official" module. Done by core developers of Tryton.
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
