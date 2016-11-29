# PSR-1

This document outlines the specifications for how dependents declare their dependencies.

This document builds on the prior information presented in [PSR-0](psr-0.md).

## Definitions

- **Library Type:** What kind of library a dependency is. Examples: plugin, theme, WordPoints module or component, WordPress, etc.
- **Relationship Type:** The kind of relationship between a dependent and a dependency.

## Overview

## Library Types

- `wordpress` - Libraries implementing the plugin API. WordPress core would be one, and any fork would also be of this type.
- `plugins` - Libraries that are installed as WordPress plugins.
- `themes` - Libraries that are installed as WordPress themes.
- `wordpoints-modules` - Libraries that are installed as WordPoints modules.
- `wordpoints-components` - Libraries that are installed as WordPoints components.

Additional types MAY be added.

## Relationship Types

There are different ways that a dependent library can relate to a "dependency", some of which don't actually imply dependency at all:

- `requires` - Things that are required for the module to work. Without them, you will experience problems, maybe even the white screen of death.
- `recommended` - Things that are recommended to use with the module. The module will work without them, but some features may be crippled, though the site won't break entirely.
- `compatible` - Things that the module is known to be compatible with. If the module is used along with them no problems should result.
- `incompatible` - Things with known incompatibilities. If the the module is used along with them it will result in problems or even break the site.
- `not-recommended` - Things with known incompatibilities. If the module is used along with them some features may be crippled, but things won't blow up.
- `integrated` - Things that aren't required, or necessarily needed for all sites, but which the module will integrate with if present. 
- `supersedes` - Things that have been replaced by this module. The module is superior and should be used instead. If the module is used along with them it will result in problems or even break the site.
- `superseded` - Things that have replaced this module. They are superior, and should be used instead. If the module is used along with them it will result in problems or even break the site.

Additional types MAY be added.

Also, each of these MAY also apply to dependency versions. For example, a module might require version 1.1 of a library, but recommend at least version 1.3.

## Syntax

``` json
{
    "required": {
        "wordpress": {
             "dot-org": {
                  "recommended": "3.9+",
                  "required": "3.7+"
             }
        },
        "plugins": { ... }
    }
}
```

In other words: `degree of dependency -> type of library -> library -> degree of dependency -> version`
