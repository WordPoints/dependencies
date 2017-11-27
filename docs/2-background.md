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

Parent-child relationships are only one type of integration though. Often two first-class plugins can integrate (call them "married"), while a child can sometimes integrate with more than one parent (only natural, we might suppose). Clear distinctions can be made between these, but in the end they are all integrations: they are all means of augmenting (and in some cases bridging) purpose-oriented packages.

## Bearing the Burden

In the end, we must acknowledge that any constraints which we want to place upon dependencies we must reasonably expect to have to apply to *most* plugins, not only a particular class built with particular intent. This is true because most plugins are extendable, that is, they can be integrated with.

In response to this realization we can decide either to design a dependency system that bears this burden, leaving the plugin developer free from contraints, or to place this burden on the plugin developer, thus releiving our depenency system. In fact, in the latter case the very design of the plugin system itself, the framework provided for building plugin APIs, could potentially bear most of that burden, rather than every plugin developer having to shouler it. 

The plugin API as it currently exists, can be reduced primarily to the action and filter API. This API was created to facilitate extension of and integration with WordPress itself. As such, it has also been suggested that it is capable of facilitating integration between plugins. However, it is acknowledged that this is only possible if plugind developers place upon themselves the same constraints which WordPress itself shoulders: "technical debt as a service." The knowledge that a differently designed API might make this unnecessary makes this pill all the harder to swallow.

## Shades of Compatibility

One of the key elements of dependency management is compatibility. It is thus worth stating that compatibility is a layered proposition. Each layer can be defined by what breaks. There is code breakage, which is the most severe. But there is also feature breakage.

## Dependency Systems

### Bundling

One form of dependency management is to bundle all dependencies with the dependent, as part of its source code. This causes problems when multiple copies of a depdency are installed, but this can be circumvented by namespacing, at least in regard to code breakage. However, when it comes to feature breakage, even namespacing is limited in its power. There is still only one filesystem, database, cache, and ultimately only one site which will be viewed in a single window. Some interactions can be prefixed, others by their very nature cannot. Just namespacing/prefixing the code itself will not insure compatibility, unless the dependencies are very careful in their interations with these things.

Nonetheless, bundling is a common form of dependency management employed within the WordPress ecosystem. 

But the fact that bundling is opaque, not only to users, but often to WordPress itself, also has consequences. Not only is the installation of the depdency tied to the installation of the dependent, so are updates. Because of this combination of factors, security patches are often not applied to bundled dependencies in a timely manner. This has lead to many hacked sites, and warrants the classification of bundling as a dangerous practice.

Bundling can work with some success for libraries, because they can be utilized for independent purposes, lessening the liklihood of conflict. However, it cannot work for extensions, because by nature they are all generally supposed to be augmenting the same feature set on the site, not separate copies of it.
