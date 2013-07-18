---
layout: post
title: "PHP Autoloading and Joomla"
date: 2013-07-18 12:00
comments: true
categories: joomla, php, dev
---

This is the first in a series of posts about component structure and autoloading within Joomla. It was originally going to all go into one, but it started to get very long very quickly. So today we're going to focus on a little bit of autoloading history, and why it's important. The next post will deal with ways to utilize the Joomla autoloader to speed up your component development.


## Some Background

But before we dig in, a little background on autoloading. Ever since version 5, PHP has had the ability to automatically locate classes on the fly. If you need to use `FooBar` class and it's not available, PHP triggers the `__autoload` function, passing in the requested class as a parameter. Once in the function, you can do some parsing of the class name and try to figure out where the file that contains the requested class is located and include it, making the class available for use. This really makes developers lives easier because you are no longer required (heh) to manually include all the files for your application in some bootstrap file. Just use classes as needed and they can be dynamically loaded.

PHP 5.1.2 offered an additional autoload function called `spl_autoload_register`. This function allows you to register mutliple callback functions onto a stack that then get's iterated over until the requested class is found. Another great win for us PHP-ers, because libraries could each contain their own optimized autoload function. PEAR had it's own autoloading convention of using underscores to represent a directory separator. Many libraries followed this convention because it was easy to understand and widely accepted.

This is where Joomla comes in. Joomla didn't choose to follow that widely accepted convention, but instead offered its own solution. For about a year and a half, Joomla has had a prefix based autoloader. Instead of using underscores to represent a directory change, the prefix autoloader uses StudlyCaps, and inserts a directory separator right before each capital letter. After inserting the directory separators, it lowercases the whole path and then appends a `.php` to the last section. It then tries to load that file to make the requested class available. One caveat to this is that if your class name is only two sections long, Joomla will duplicate the last section when loading from the filesystem. So class `FooBar` becomes `foo/bar/bar.php` when loading the file.

## Using the Autoloader

Registering your own prefix for autoloading classes is fairly simple. The function has three parameters; two required and one optional. The two required parameters are `$prefix` and `$path`. The prefix is the part of the class that let's the autoload function know what group of paths to look in, and the path parameter adds a lookup path to the array for the specified prefix. I know that sounds complicated, but hopefully an example will help. When registering a prefix to the autoloader, you would do something like this:

<pre>
JLoader::registerPrefix('User', JPATH_LIBRARIES . '/user');

// To register an additional path. Could be used for overrides.
JLoader::registerPrefix('User', dirname(__FILE__) . '/overrides/user');
</pre>

What this code does is tell the autoloader to look in `JPATH_LIBRARIES . '/user'` any time a class that begins with `User` is used. With that in mind, where do you think the file that contains the class `UserModelData` would be located? If you guessed `JPATH_LIBRARIES . '/user/model/data.php'`, you would be correct. But remember, since we registered a second lookup path for the `User` prefix, the file may also be located there.


## PHP-FIG, Autoloading and the Future

Joomla is a member project of the [PHP-FIG](http://php-fig.org). The FIG codifies common practices for PHP that help to increase project interoperability. One of its accomplishments is the redification of [PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md), the autoloading standard. PSR-0 maps native PHP Namespaces to directory structure (much like PEAR used underscores) when autoloading from the filesystem. It is the widely accepted standard for autoloading in PHP projects. While Joomla core is still a long way from using the full PSR-0 standard (since it relies on native namespaces, which aren't yet used in core code), the project has made available a PSR-0 compliant autoloader for you to use in your extensions, starting with Joomla CMS version 3.2. What's great about this is it allows you to utilize 3rd party code that makes use of native PHP Namespaces. 

That last paragraph there was just an FYI. The next post in this series isn't going to cover using PSR-0 for autoloading your component classes and in fact, it couldn't without re-writing a good chunk of the Joomla API. Expect the next post in this series hopefully within the next week.


