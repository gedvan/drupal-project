# Template for Drupal 9 projects

A template for Composer managed Drupal 9 projects.

Based on [drupal/recommended-project](https://github.com/drupal/recommended-project) and on [drupal-composer/drupal-project](https://github.com/drupal-composer/drupal-project).

## Usage

Install composer

Create project

## Document Root

The web server's document root must point to `web` directory. That's where Drupal will be installed.

You can modify this by changing every reference to `web` folder on `composer.json` and `.gitignore` files (*not tested!*).

## Modules and themes

To install contributed modules and themes, you shall run:

```shell
$ composer require drupal/<name>
```

Where `<name>` is the machine name of the project (module or theme) to be installed. They will be downloaded to `web/modules/contrib` or `web/themes/contrib`, respectively. After that, you can enable them on Drupal administration pages or by Drush commands.

To remove, first uninstall them from Drupal and then run `composer remove drupal/<name>`;

Any custom module or theme should be placed on `web/modules/custom` or `web/themes/custom`, respectively. Those directories are not ignored by git and will be asked to be added to the repository.

## Libraries

Some modules and themes rely on third-party libraries that must be placed on the Drupal's libraries folder (`web/libraries`). To do that, you have to tell Composer which packages you want to place there. This is done in `extra.installer-paths` section of `composer.json`.

For example, to install the `twbs/bootstrap` package to the `web/libraries` folder, you should change the `composer.json` to the following:

```json lines
  ...
  "extra": {
    ...
    "installer-paths": {
      "web/core": ["type:drupal-core"],
      "web/libraries/{$name}": ["type:drupal-library", "twbs/bootstrap"],
      "web/modules/contrib/{$name}": ["type:drupal-module"],
      ...
```

After that, you can run `composer require twbs/bootstrap`.

You can do that to any package available on [packagist.org](https://packagist.org/). But if you need a library that doesn't exist on that, you can try the [Asset Packagist](https://asset-packagist.org/). It is a Composer-compatible repository that allows you to include Bower and NPM packages into your PHP project.

To enable the Asset Packagist, first add it to the `repositories` section of `composer.json`:

```json lines
  ...
  "repositories": [
    {
      "type": "composer",
      "url": "https://packages.drupal.org/8"
    },
    {
      "type": "composer",
      "url": "https://asset-packagist.org"
    }
  ],
  ...
```

After that, add to the `extra` section the `installer-types` entry as an array with values `bower-asset` and/or `npm-asset`, as demonstrated below. You can also configure the installation path to that type of packages (in this case, to the libraries folder).

```json lines
    "extra": {
        ...
        "installer-types": ["bower-asset", "npm-asset"],
        "installer-paths": {
            ...
            "web/libraries/{$name}/": ["type:drupal-library", "type:bower-asset", "type:npm-asset"]
            ...
        }
    }
```

Finally, you can `composer require` the package, like `composer require bower-asset/chosen`.

To get more information and check out other options about custom installation paths, please visit [composer/installers](https://github.com/composer/installers) and [oomphinc/composer-installers-extender](https://github.com/oomphinc/composer-installers-extender).

## Updating Drupal core

To update Drupal core, run:

```shell
$ composer update --with-dependencies drupal/core-recommended drupal/core-composer-scaffold
```

## Scaffold files

As we can see on `instaler-paths` configuration, the Drupal core itself (`drupal/core`) is placed on `web/core` folder. But, the Drupal installation needs some files to be placed outside of this folder, like on web root (`index.php`, `update.php`, `.htaccess`, `robots.txt`, etc.). This is done by a Composer plugin, the [Drupal Composer Scaffold](https://github.com/drupal/core-composer-scaffold) (`drupal/core-composer-scaffold`).

These files are copied to the desired place during `composer install` and they don't need to be added to the version control (thay are listed on `.gitignore`).  If you need to change or overwrite some of them, check the instructions on plugin page.

## Patching

## Drush

Drush is already listed as a development dependency on `composer.json`. So, it should be installed during the `composer install`(unless you specify the `--no-dev` option). You can run it by the path `./vender/bin/drush`. Example:

```shell
$ ./vendor/bin/drush status
```

If you don't want to type the relative path, install the [Drush Launcher](https://github.com/drush-ops/drush-launcher). It's a simple wrapper that will allow you to simply type `drush` on command line and it will find and execute the project specific version of drush located in your `vendor` directory.
