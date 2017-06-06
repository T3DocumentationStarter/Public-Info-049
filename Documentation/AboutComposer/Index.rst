
.. include:: ../Includes.txt

==============
About Composer
==============


What is Composer?
=================

From `getcomposer.org <https://getcomposer.org/doc/00-intro.md>`__:

   Composer is a tool for dependency management in PHP. It allows you to 
   declare the libraries your project depends on and it will manage 
   (install/update) them for you.


See also:

- https://composer.typo3.org/
- https://packagist.com/
- https://wiki.typo3.org/Composer


Available TYPO3 extension packages
==================================

.. rst-class:: horizbuttons-primary-xxl

- `list and search extensions <https://composer.typo3.org/satis.html>`__



.. highlight:: json

Example: Load extension `bk2k/bootstrap-package`::

   {
      "name": "marble/t3dev8",
      "description": "Trying composer",
      "type": "project",
      "require": {
         "typo3/cms": "8.7",
         "helhum/dotenv-connector": "^2.0",
         "bk2k/bootstrap-package": "*"
      },
      "license": "MIT",
      "authors": [
         {
            "name": "Martin Bless",
            "email": "martin.bless@mbless.de"
         }
      ],
      "minimum-stability": "stable",
      "extra": {
         "typo3/cms": {
            "web-dir": "public_html"
         }
      },
      "repositories": [
         {
            "type": "composer",
            "url": "https://composer.typo3.org"
         }
      ]
   }



Writings
========

- Blogpost: `How to install TYPO3 using composer (in less than 5 minutes)
  <https://typo3.com/more/blog/how-to-install-typo3-using-composer-in-less-than-5-minutes/>`__, 2017-04-10
  
- Blogpost (de): `Composer zur Installation und Verwaltung von TYPO3 verwenden
  <https://www.clickstorm.de/blog/composer-installation-verwaltung-typo3/>`__, 2017-01-05
