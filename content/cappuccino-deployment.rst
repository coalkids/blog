How to deploy your Cappuccino application ?
###########################################

:date: 2013-02-27 10:14
:tags: coalkids
:slug: cappuccino
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




