---
title: Using Meta Box with Composer
---

[Composer](https://getcomposer.org) is a package dependency manager for PHP. It's kind of [TGM Activation class](http://tgmpluginactivation.com) for WordPress, but works for any PHP package. Composer allows us to include other PHP libraries in our projects without conflict between them. Without Composer, if a project requires 2 libraries A and B, and both of them requires a library C, then C might be included twice. The scenario can get worse if A and B includes different version of C which can cause a conflict when loading the library. Composer helps us to avoid this issue.

Besides, there are more than 100K packages for us to explore if we use Composer. They can be a framework like Laravel, Symphony or a package to handle the payment with Stripe or authenticate with social networks. That means lots of possibility to do something with less code by standing on shoulders of giants!

Fortunately, **Meta Box is totally compatible with Composer** and you can use it as a library for your projects. As Meta Box is a WordPress-specific package, the "project" term here means a WordPress website, plugin or theme. This tutorial will show you how to use it for a plugin. I will name the plugin **MB Composer Example** and its code is [available on Github](https://github.com/wpmetabox/mb-composer-example).

## Installing Composer

Please follow the [very detailed guide](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx) on the Composer homepage to know how to install Composer on your system.

## Project setup

Composer requires a config file named `composer.json` where we defines all the dependencies. So, create it in the plugin folder and add the following content:

```json
{
  "require": {
    "rilwis/meta-box": "4.*"
  }
}
```

The first (and often only) thing you specify in `composer.json` is the `require` key. You're simply telling Composer which packages your project depends on. The package name has the format `author/package-name`. In this case, it's `rilwis/meta-box`.

The value is the version you want to use in your project. `4.*` means the latest version 4. You can set a specific version `4.8.7` or using wildcard. For more info about the syntax of versioning, please follow the [Composer documentation](https://getcomposer.org/doc/articles/versions.md).

You can add more packages by adding more key-value pairs if your project uses other packages than Meta Box.

## Installing dependencies

The 2nd step is telling Composer to pull the packages that your project depends on to the local folder. Simply `cd` to your project folder and run:

```bash
composer install
```

Make sure you install Composer globally. If you don't know how to do that, please refer to the documentation in the Installing Composer section above.

After a minute, Composer will download Meta Box and put it in a local folder. Your folder structure after that will look like this:

![meta box composer](https://i.imgur.com/22242cz.png)

## Autoloading

To make Meta Box works, we need to include the plugin's file in our plugin. Although we can [hardcode](/include-meta-box-plugin-themes/#section-include-meta-box-directly) that, Composer has an intelligent autoloading mechanism to load packages. Simply put the following line in the plugin's main file:

```php
require plugin_dir_path( __FILE__) . 'vendor/autoload.php';
```

I use `plugin_dir_path` function because this is a WordPress environment. But you can ignore it and use a simpler version like this:

```php
require __DIR__ . 'vendor/autoload.php'; // PHP 5.3

// or

require 'vendor/autoload.php'; // Relative path inclusion
```

Now Meta Box is available in your plugin and we can start using it.

## Register meta boxes and custom fields

As Meta Box is loaded, we can register meta boxes and custom fields as usual. In this example, I register a demo meta box with 2 fields of Name and Bio:

```php
add_filter( 'rwmb_meta_boxes', 'mb_composer_example_register_meta_boxes' );
function mb_composer_example_register_meta_boxes( $meta_boxes ) {
    $meta_boxes[] = array(
        'title'  => __( 'A sample meta box', 'mb-composer-example' ),
        'fields' => array(
            array(
                'name' => __( 'Name', 'mb-composer-example' ),
                'id'   => 'name',
                'type' => 'text',
            ),
            array(
                'name' => __( 'Bio', 'mb-composer-example' ),
                'id'   => 'bio',
                'type' => 'wysiwyg',
            ),
        ),
    );

    return $meta_boxes;
}
```

Now go to **Dashboard > Posts > Add New** and we'll see:

![meta box](https://i.imgur.com/0RTrNFK.png)

Bravo! We made it.

## Conclusion

This tutorial is a very simple example of using Meta Box with Composer. You can learn more about Composer by reading its [documentation](https://getcomposer.org/doc/) and [browser packages at packagist.org](http://packagist.org/). Although this is not a traditional way to develop plugins for WordPress, using a modern PHP tool like Composer benefits us a lot because of 2 reasons:

- Dependency management: it's always a headache for managing dependencies in WordPress. There are some works around that like TGM Plugin Activation class and Plugin Dependency. But it's still far from perfect. Not all plugins are be able to be included inside another plugin like Meta Box.
- Vast amount of libraries: this is a huge advantage of using Composer. With these well-coded libraries, your development process will be easier, faster and thus, it saves you tons of time.

So, enjoy Composer and Meta Box! And share with us how you use them in your projects via the Contact form. We'd love to know that!