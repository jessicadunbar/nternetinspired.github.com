---
layout: post
title: "Structuring your Joomla Component for Autoloading"
date: 2013-07-19 14:51
comments: true
categories: joomla, php, dev, component
---

In [my last post](/php-autoloading-and-joomla/), I went over the basics of how PHP (and specifically Joomla) handles autoloading classes when you need them. If you are new to PHP or Joomla and haven't yet read it, I would do that before continuing.

## Current Situation

Currently, Joomla's autoloading facilities are reserved for core libraries. The components that ship with the CMS do not make use of JLoader for anything. In fact, the structure of the core components really prevents that from happening. A typical core Joomla Component is structured like this: (truncated for brevity)

<pre>
components/com_content   // Main Component Folder
|- content.php           // Component entry file.
|- controller.php        // class: ContentController
|- controllers/          // Holds component controllers
|  `- article.php        // class: ContentControllerArticle
|- helpers/              // Holds helper classes
|  `- article.php        // class: ContentHelperArticle
|- models/               // Holds component models
|  `- article.php        // class: ContentModelArticle
|- router.php            // Component router file
`- views/                // Holds component views
   `- article/           // The article view
      |- tmpl/           // Holds layouts within the article view
      |  `- default.php  // The default layout for the article view.
      |- view.html.php   // class: ContentViewArticle
      `- view.json.php   // class: ContentViewArticle
</pre>

Since you've read my previous post about typical autoloading in Joomla, you should have no problem seeing why this current structure prevents you from using `JLoader::registerPrefix('Content', JPATH_SITE . '/components/com_content');` to be able to autoload all these classes. If you tried to instantiate a controller using `$controller = new ContentControllerArticle`, it would look for the file in `com_content/controller/article.php`. That path, however, does not exist. A typical component structure calls for you to have your controller classes in a folder named `controllers` instead of the singular (and that which matches your controllers name) `controller` folder. So trying to use that errors out.

In order for Joomla to know to look in the `com_content/controllers` folder instead, you are required to use `JControllerLegacy::getInstance('Content');`. That method is environment aware and knows what component you are currently executing, and it looks within the component root folder for a controllers folder, and then loads in the proper controller file. The downside to this approach is that your IDE has no way of knowing what methods are available in your controller, since you're using a proxy method to access it, instead of doing direct instantiation. That's a problem.

Having this happen on controllers might not be so bad, but it's exactly the same with all the other pieces of your component as well. You have to register a model lookup path to your controller, and then use (from within your controller) `$this->getModel('Foo')` in order to access it. And the same IDE limitations apply here as well. Needless to say, this is not a good way to structure your code.

Some may think that using the `JControllerLegacy::getInstance()` method is great since it's environment aware and you don't have to any extra setup - things just work. I would disagree. What if you built a robust component that had multiple views, some plugins and a few modules for good measure. Let's assume one of those modules needed data access, and it would be really helpful if it could use one of your models. Currently, you only have a few choices.

- Manually include the model file. (bleh)
- Duplicate the code from your model to your module helper class. (bleh)
- Use `JLoader::register()` to directly register the single class you need. (better than the other 2, but meh. What if requirements change?)

The best solution, in my opinion, is to properly structure your component from the start.

## What Now?

So you may be thinking to yourself, "Well Don, that sounds good and all, but how do I accomplish this? Plus, I gotta support 2.5 as well as 3.x, so I can't do anything that will make that task harder." Well no worries, friend. Joomla has supported the prefix autoloader since 2.5, and everything I'm going to show you in this series works on 2.5+, including the 3.x series.

The basic answer, and the one I'm going to leave you with today, is that you need to propertly structure your component so that autoloader can use that structure to find your classes. Later on, I'm going to go over some advanced techniques that will help you modularize your component and make bits of it available throughout your system, so you can build a truly integrated web application, instead of trying to force 3rd party components to work nicely together.

So what you need to know for now is what the "proper" component structure looks like. I leave you with that below.


<pre>
components/com_content   // Main Component Folder
|- content.php           // Component entry file.
|- controller/           // Holds component controllers
|  |- article.php        // class: ContentControllerArticle
|  `- controller.php     // class: ContentController
|- helper/               // Holds helper classes
|  `- article.php        // class: ContentHelperArticle
|- model/                // Holds component models
|  `- article.php        // class: ContentModelArticle
|- router.php            // Component router file
`- view/                 // Holds component views
   `- article/           // The article view
      |- tmpl/           // Holds layouts within the article view
      |  `- default.php  // The default layout for the article view.
      |- html.php        // class: ContentViewArticleHtml
      `- json.php        // class: ContentViewArticleJson
</pre>

Looks easy, right? There are a few gotchas, and I'll cover those in the next article. Have any questions? That's what the comments section is for.
