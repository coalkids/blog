How to deploy your Cappuccino application ?
###########################################

:date: 2013-03-03 20:59
:tags: jake, deploy, press, flatten, nginx, gzip
:category: cappuccino
:slug: cappuccino-deployment
:author: Christophe Serafin
:lang: en

Cappuccino is an open source framework that helps developers to build web
applications. It is based on the Objective-J (J stands for Javascript) language which is the equivalent of Objective-C in Cocoa framework.

To find more information about Cappuccino framework, I would suggest you to visit their website (http://www.cappuccino-project.org/).

From this article, I would like to explain the deployment part which can become a little bit tricky.

Using Jake
==========

Jake is similar to Rake in the Ruby on Rails world. Based on a JakeFile, this command line tools let you compile your Objective-J code (https://github.com/280north/jake)

For instance, you will be able to build your application using ::

    jake build

or ::

    jake release

Here we will focus on the ``jake deploy`` command line.

Press
=====

Press's goal is to remove unnecessary code by only retaining files which are
actually used in your application. It tries to follow your imports to determine
which files should be removed.

For more information about press : https://github.com/cappuccino/cappuccino/wiki/press

In your application, you might have created your own libraries with a single
Objective-J files to import all your library or categories content. ::

    @import "Categories/Categories.j" // Will exclude all files imported in Categories.j

It appears that sometimes, press excludes your library code. To avoid that, you
should import your files directly in the AppController.j : ::

    @import "Categories/CPView+Animations.j"
    @import "Categories/CPView+Styles.j"
    @import "Categories/CPWindow+Animations.j"
    @import "Categories/CPWindow+Styles.j"


Flatten
=======

Flatten inlines all your code into one or more Javascript Files. It comes with different compresssors and can optionnaly split files into 2 to 6 files to optimize the browser download.

For more information about flatten :
https://github.com/cappuccino/cappuccino/wiki/flatten

Hosting your application
========================

Basicaly, all you need to host a Cappuccino application is a simple static http server.

However, Cappiccino applications are not very lightweight, so you might want to tune your http server settings in order to make your application loading faster.

If you use flatten through your application deployment, you will end up with a bunch of Application.js files and a Ressources folder.

Javascript files are simple text files that are good subject for gzip compression. You might reduce your Application.js files up to 65% just by applying a simple gzip compression.

If you use nginx to serve your application, use the following settings to activate gzip compression : ::

    gzip on;
    gzip_types application/x-javascript text/javascript application/octet-stream;
    gzip_http_version 1.0;

The ``gzip_types`` directive let you enable gzip compression for other mime-type than text/html (which is enabled by default). For your Cappuccino application you will need ``application/x-javascript`` and ``text/javascript``  mime-types support for you ``.js`` file. We add the ``application/octet-stream`` mime-type for ``.sj`` files.

We use the ``gzip_http_version`` directive because we are hosting most of our application on dotcloud which is transporting trafic between their routers and our Nginx servers in HTTP/1.0 (see http://answers.dotcloud.com/question/212/gzip-compression).

You can find documentation to enable gzip compression on an Apache server on the Cappuccino wiki : https://github.com/cappuccino/cappuccino/wiki/Optimizations

.. gzip + press + flatten
   Apparition spinner : 12,2s
   Total : 17,89s
   onload : 13,24s
   DOMContentLoaded : 9,77s
   838KB transferred
   2457,6KB
   diff : 1619,6KB

.. gzip + press :
   Apparition spinner : 7,5s
   Total : 30,85s
   onload : 7,83s
   DOMContentLoaded: 4,17s
   944KB transferred

.. 2483974  1 mar 18:06 Application.js
   349036  1 mar 18:05 Application.js.gz
   326991  1 mar 18:05 Application.js.gz.zopfli

.. 2483974 100
   349036	14%
   326991	13,16%