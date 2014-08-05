Mathielen Import Engine Bundle
==========================

Introduction
------------
This is a bundle for the [mathielen/import-engine library](https://github.com/mathielen/import-engine).
It provides an easy way to configure a full-blown data importer for you symfony2 project.

Installation
------------
This library is available on [Packagist](https://packagist.org/packages/mathielen/import-engine-bundle):

To install it, run: 

```bash
$ composer require mathielen/import-engine-bundle:dev-master
```

Then add the bundle to `app/AppKernel.php`:

```php
public function registerBundles()
{
    return array(
        ...
        new Mathielen\ImportEngineBundle\MathielenImportEngineBundle(),
        ...
    );
}
```

Configuration
------------
Add your importer configurations in your `app/config/config.yml`

```php
mathielen_import_engine:
    #configure storageproviders, that are used in all importers
    storageprovider:
        type: upload                        #[upload, service, array, doctrine, localfile]
        path: "%kernel.root_dir%/Resources/import"
    #configure your Importers
    importers:
        your_importer_name:
            #automaticly recognize this importer by availability of the conditions below
            preconditions:                  #the conditions below can be used
                format: excel               #format of file must be [csv, excel, xml, ...]
                fieldcount: 2               #must have this number of fields
                fields:                     #these fields must exist (order is irrelevant)
                    - 'header2'
                    - 'header1'
                fieldset:                   #the discovered fields must exist exactly this way
                    - 'header1'
                    - 'header2'
                filename: 'somefile.xls'    #filename must match one of these regular expression(s) (can be a list)
            #use a object-factory to convert raw row-arrays to target objects
            object_factory:
                type: jms_serializer        #[jms_serializer, ~]
                class: Acme\DemoBundle\ValueObject\MyImportedRow
            #validate imported data
            validation:
                source:
                    - myconstraint          #add constraints to source fields
                target: ~                   #validate against generated object from object-factory (target)
            #target of import
            target:
                type: service               #[service, array, doctrine, localfile]
                service: import_service     #service name in DIC
                method: processImportRow    #method to invoke on service
```