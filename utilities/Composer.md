# Composer

http://code-artisan.io/phper-all-you-need-to-know-about-composer/

Composer is a dependency manager for PHP (*the* dependency manager for PHP in effect).  

Note the distinction between dependency management and package management. Dependency management applies to libraries (per project), whereas package management applies to system modules. So, in PHP terms a system module is something like PDFlib, which confers system-wide pdf handling capabilities to your php installation, but a library is something like Doctrine - installed per project, and quite possibly in different versions per project. Whilst this distinction exists, people often talk about 'packages' in relation to dependency management, and that's what muddies the water.

I've often used Composer in a kind of 'rote' way to install frameworks and components, and also used the Composer autoloader. The autoloader is a sort of side-benefit of Composer. It gives you a ready made autoloader that you can use for your own classes.

## Installation

To use Composer in Windows, you download and install it on your PC, then set PATH environment variable so that Windows can find it from anywhere, then - from anywhere - open windows Command Prompt or some other shell like Git Bash and type in composer commands.

## Deployment in a new project

### Create composer.json

Get your shell of choice up and running in the folder you want your new project to live in and type `composer init`. You'll be asked a series of questions the answers to which generate the initial composer.json file for your project. 

Many of these settings are optional e.g. name, description, licence and so on. You'd want to use these in a release project but probably not in development and testing. You will also get prompted to add dependencies interactively/manually at this stage. This would populate the *require* and *require-dev* sections of composer.json. If you just bypass these for now you get an empty *require* section. So you might end up with something like this:

```
{
    "name": "tom/drupal7",
    "description": "Learn Drupal 7",
    "minimum-stability": "stable",
    "require": {}
}
```

Now we can add dependencies by editing this file. We might end up with a *require* section like this:

```
"require": {
       "php": ">=5.6.4",
       "symfony/symfony": "3.6.*"
   },
   "require-dev": {
       "phpunit/phpunit": "~5.0",
   }
```

This defines *exactly* what the dependencies of the project are. So in this case, PHP 5.6.4 or higher (in production and dev), any 3.6 version of symfony, and - in dev - any version of phpunit higher than 5.0 and less than 6.0 (I think that's right - anyway tilde defines a range. But I don't see how ~5.0 differs from 5.*). All this depends on third parties following the rules of version numbering correctly, i.e. using semantic versioning 'major.minor.patch'.

### Install from composer.json

Once composer.json is created, use `composer install` from the shell prompt to install the specified dependencies (NB the term 'dependency' here is a sort of generalised abstraction but it's useful because there isn't a suitable concrete term. Terms like 'module', 'package', 'component' are too specific and none of them properly encompasses all of the things you might manage with Composer).

Composer fetches the required dependencies and installs them in a *vendor* folder. It also creates the file *composer.lock*. This file is a catalogue of the exact versions of all the dependencies installed. Any future install of Composer will refer to this file.

## Global v local

From installing Drush on my Windows machine... with Composer installed in Windows, if you then open a shell from anywhere and type `composer global require drush/drush:7.*` this will install Drush globally and update the global composer.json file located in the global Composer folder (at Users\Tom\AppData\Roaming\Composer in my case). If you omit 'global' and type `composer require drush/drush:7.*` then Drush will be installed in the current folder - i.e. you'll see a drush folder within vendor, and that will contain all the Drush stuff including executables - and the composer.json in that folder will be updated (or created if not already present).

How would you choose to install a utility like Drush locally v globally? I suppose a global installation is cleaner, but a local installation is more portable?



