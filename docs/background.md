# Background

This document explores the background of the dependencies concept as it relates to WordPress.

## Two Different Things

The traditional concept of components or code libraries is that they are reusable units providing a standard feature that multiple plugins can utilize. In essence, they are about *standardization* and *reducing duplication*. Rather than reinventing the wheel, a single library can provide a feature, and provide it well.

On the other hand we have the concept of extensions, children that all *integrate with a common parent*. The parent plugin provides a feature set which is *extended* by other packages. This relationship is exactly the opposite of that between a plugin and a library. Here, the plugin is providing a central feature set that is being utilized by other packages, rather than itself depending on a feature set provided by a library.

However, it is not as simple as just saying that a plugin with extensions is really just a library.

Because of the nature of WordPress's code design, it is possible for extensions to be created for almost any plugin. Thus, a plugin can exist as a first-class citizen, not created speficially to be a library, and yet still be extended even without its knowledge, in ways that it may not even foresee.

Extensions in this case are not *utilizing* the plugin as a library, they are actually *extending* it. While sometimes the distinction between utilization and extension can seem blurry, the difference is quite significant. In each case something is built on top of something else, but the *purpose* of a plugin is *independent* of the library it is built on, whereas the *purpose* of an extension is *directly related* to that of the plugin which it extends.

A library is designed specifically with re-use in mind, and as such that is its ultimate purpose: to be a library. Its purpose is to be a library providing a particular feature, yes. But the ultimate task or goal which people will use that feature to acheive on their site is entirely irrelevant to the library's existence. Whereas an extension designed to augment a particular feature that a site will use to fulfill a particular set of purposes. For a library the end-goal need never be known (and ideally is in fact not known), whereas an extension is almost always aware of the purpose it must help acheive; indeed, the value of an extension is often in the fact that caters to a particular niche in the market.

Of course, the actual comparison must be between the library and the plugin being extended. And here again we see that a plugin is built to help a site owner acheive a particular set of goals. There is no such thing as a generic plugin, but libraries are designed to provide a generic implementation of a feature set that can be widely reused.

## Integrations

Parent-child relationships are only one type of integration though. Often, two first-class plugins can integrate (call them "married"), while a child can sometimes integrate with more than one parent (only natural, we might suppose). Clear distinctions can be made between these, but in the end they are all integrations: they are all means of augmenting (and in some cases bridging) purpose-oriented packages.

## Bearing the Burden

In the end, we must acknowledge that any constraints which we want to place upon dependencies we must reasonably expect to have to apply to *most* plugins, not only a particular class built with particular intent. This is true because most plugins are extendable, that is, they can be integrated with.

In response to this realization we can decide either to design a dependency system that bears this burden, leaving the plugin developer free from contraints, or to place this burden on the plugin developer, thus releiving our depenency system. In fact, in the latter case the very design of the plugin system itself, the framework provided for building plugin APIs, could potentially bear most of that burden, rather than every plugin developer having to shoulder it. 

The plugin API as it currently exists can be reduced primarily to the action and filter API. This API was created to facilitate extension of and integration with WordPress itself. As such, it has also been suggested that it is capable of facilitating integration between plugins. However, it is acknowledged that this is only possible if plugin developers place upon themselves the same constraints which WordPress itself shoulders: "technical debt as a service." The knowledge that a differently designed API might make this unnecessary makes this pill all the harder to swallow.

## Shades of Compatibility

One of the key elements of dependency management is compatibility. It is thus worth stating that compatibility is a layered proposition. Each layer can be defined by what breaks. There is code breakage, which is the most severe. But there is also feature breakage.

## General Problems

One general problem that any dependency system can have is that it places additional cognative strain on the user to try and comprehend the dependency web. This is especially true when dependencies are declared that are not actually needed. An additional problem that stems from having many dependencies is the possible decrease in site performance, just from the sheer amount of code.

The reality, however, is that all of these problems already exist, without a dependency system. In the case of libraries in particular, a dependency system provides a solution, by allowing the possibility of hiding libraries from the average user, and encouraging the honing of a single canonical package to be as performant and secure as possible. 

## Dependency Systems

### Bundling

One form of dependency management is to bundle all dependencies with the dependent, as part of its source code. This causes problems when multiple copies of a depdency are installed, but this can be circumvented by namespacing, at least in regard to code breakage. However, when it comes to feature breakage, even namespacing is limited in its power. There is still only one filesystem, database, cache, and ultimately only one site which will be viewed in a single window. Some interactions can be prefixed, others by their very nature cannot. Just namespacing/prefixing the code itself will not ensure compatibility, unless the dependencies are very careful in their interactions with these things.

Nonetheless, bundling is a common form of dependency management employed within the WordPress ecosystem. 

But the fact that bundling is opaque, not only to users, but often to WordPress itself, also has consequences. Not only is the installation of the dependency tied to the installation of the dependent, but so are updates. Because of this combination of factors, security patches are often not applied to bundled dependencies in a timely manner. This has lead to many hacked sites, and warrants the classification of bundling as a dangerous practice.

Bundling can work with some success for libraries, because they can be utilized for independent purposes, lessening the liklihood of conflict. However, it cannot work for extensions, because by nature they are all generally supposed to be augmenting the same feature set on the site, not separate copies of it.

### None/Hooks

As noted above, the most common reply to the suggestion of a dependency system for WordPress, is to say that one is not really needed, if a plugin properly uses the APIs provided by WordPress already. In particular, the hooks API with its actions and filters can be used to run code only when it is triggered by the plugin that it is integrating with. (Note that this does not solve the other half of the dependency equation, which is libraries, however.)

One of the key benefits of this approach is its granularity. It allows a dependent to degrade gracefully, from fully working with a dependency, to not working with it at all, or most anywhere in between. On the flip side of this is the fact that the degree of compatibility attained is less transparent to the user: just because their site wasn't killed doesn't mean that the features being augmented are actually going to work properly. Of course, this can be remedied in part by the dependent plugin, by displaying warning messages, but even then it may be difficult for it to tell if an integration is actaully being fully realized. Mainly, it can just give a warning when a dependency is not present at all.

This approach also avoids some of the problems and pitfalls that a "proper" dependency system of the form usually suggested would run into. These include complexity, and the difficulty of uniquely identifying dependencies.

The primary problem with this approach, however, has been that it is optional, and the implementation is entirely dependent upon the plugin developer. As a result, many plugins have struggled with this, or taken a different route. Even among plugins that follow this philosophy, there are many pitfalls that are seldom considered.

For example, consider a case where an extended plugin deprecates and then later removes a particular function. Just because dependents have hooked their code to an action provided by the parent plugin does not mean that all functions provided by it will always be available. This is why it is necessary for the extended plugin to practice "technical debt as a service," and take great care in ever removing code.

This can be eased both by the dependent and dependency, however. The dependent can always check for the existence of the dependency's functions before using them. And the dependent can endeavor to deprecate actions and filters along with the functions that are likely being used with them, although this will not always be feasible.

In short, this is a two-pronged strategy, that requires great attention by the plugin developer both when building their plugin as well as when modifying it in the future. This raises the bar for plugin development, and although it is primarily a concern of plugins that are built specifically with extension in mind, any plugin can be roped into a dependency web without foreseeing the responsibility that will come with it.

### Declared Dependencies

The most commonly suggested basis for a WordPress dependency system is for plugin's to declare their dependencies, probably in the plugin header.

This could also have applications beside basic dependency management, like a search feature for finding extensions for a particular plugin.

A downside to this approach is the potential for complexity. 

There is also an argument against adding an explicit system of this sort, becuase developers will feel that the onus is no longer on them to try to ensure their plugin fails gracefully when a dependency isn't installed. The usefulness of this approach is therefore blunted when it is not accompanied by a dependency management framework and used in the context of a software design that specifically favors graceful (dis)integration.

## Resources

### WordPress Dependency Managers

- [WP Plugin Dependencies by XWP](https://github.com/xwp/wp-plugin-dependencies/)
- [TGM Plugin Activation](http://tgmpluginactivation.com/)
- [My Plugin Manager](http://mypluginmanager.com/)
- [Mozart](https://github.com/coenjacobs/mozart)
- [WordPress Composer Installer](https://github.com/coenjacobs/wordpress-composer-installer)
- [WP Dependency Installer](https://github.com/afragen/wp-dependency-installer)
- [oik-lib](https://github.com/bobbingwide/oik-lib)
- [Composer](http://composer.rarst.net/)

### Related WordPress Tickets
- [#10190](https://core.trac.wordpress.org/ticket/10190) Plugin Dependency Class API
- [#11308](https://core.trac.wordpress.org/ticket/11308) Handling plugin dependencies
- [#12612](https://core.trac.wordpress.org/ticket/12612) Plugins dependancy
- [#13296](https://core.trac.wordpress.org/ticket/13296) Plugin queue to match scripts and styles
- [#22316](https://core.trac.wordpress.org/ticket/22316) Plugin Dependencies (Yet Another Plugin Dependencies Ticket) 
- [#30550](https://core.trac.wordpress.org/ticket/30550) Add plugin dependency support
- [#42383](https://core.trac.wordpress.org/ticket/42383) Creating "Frameworks" in "wp-content"

### Blog Posts
- [Plugin Dependency for WordPress Themes](http://www.onequarterenglish.co.uk/plugin-dependency-for-wordpress-themes)
- [WordPress Plugin Dependency Programming](http://www.cleancode.co.nz/blog/481/wordpress-plugin-dependency-programming)
- [WordPress needs plugin dependencies](http://lucdebrouwer.nl/wordpress-needs-plugin-dependencies/)
- [A Case For Dependency Management with WordPress](https://tommcfarlin.com/dependency-management-with-wordpress/)
- [WordPress Core Proposal: Dependency Management](https://ttmm.io/tech/wordpress-dependency-management/)
- [Core plugin dependency is necessary for a better WordPress ecosystem](https://poststatus.com/core-plugin-dependency-is-necessary-for-a-better-wordpress-ecosystem/)
- [Ryan McCue: How I Would Solve Plugin Dependencies](https://journal.rmccue.io/322/plugin-dependencies/)
- [Gary Pendergast: How I Would Solve Plugin Dependencies](https://pento.net/2015/05/24/plugin-dependencies/)
- [[oik] plugins: How I would solve plugin dependencies](https://www.oik-plugins.co.uk/2015/08/how-i-would-solve-plugin-dependencies/)

### Other Dependency Systems

- [Drupal](https://www.drupal.org/docs/7/creating-custom-modules/writing-module-info-files-drupal-7x#dependencies)
- [Debian](https://www.debian.org/doc/debian-policy/#document-ch-relationships)
