# PSR-1

This document outlines the specifications for how dependents declare their dependencies.

This document builds on the prior information presented in [PSR-0](psr-0.md).

## Definitions

- **Library Type:** What kind of library a dependency is. Examples: plugin, theme, WordPoints extension or component, WordPress, etc.
- **Relationship Type:** The kind of relationship between a dependent and a dependency.

## Overview

## Library Types

- `wordpress` - Libraries implementing the plugin API. WordPress core would be one, and any fork would also be of this type.
- `plugins` - Libraries that are installed as WordPress plugins.
- `themes` - Libraries that are installed as WordPress themes.
- `wordpoints-extensions` - Libraries that are installed as WordPoints extensions.
- `wordpoints-components` - Libraries that are installed as WordPoints components.

Additional types MAY be added.

## Relationship Types

There are different ways that a dependent library can relate to a "dependency", some of which don't actually imply *dependency* at all:

- `requires` - Things that are required for the extension to work. Without them, you will experience problems, maybe even the white screen of death.
- `recommended` - Things that are recommended to use with the extension. The extension will work without them, but some features may be crippled, though the site won't break entirely.
- `compatible` - Things that the extension is known to be compatible with. If the extension is used along with them no problems should result.
- `incompatible` - Things with known incompatibilities. If the the extension is used along with them it will result in problems or even break the site.
- `not-recommended` - Things with known incompatibilities. If the extension is used along with them some features may be crippled, but things won't blow up.
- `integrated` - Things that aren't required, or necessarily needed for all sites, but which the extension will integrate with if present. 
- `supersedes` - Things that have been replaced by this extension. The extension is superior and should be used instead. If the extension is used along with them it will result in problems or even break the site.
- `superseded` - Things that have replaced this extension. They are superior, and should be used instead. If the extension is used along with them it will result in problems or even break the site.

Additional types MAY be added.

Also, each of these MAY also apply to dependency versions. For example, a extension might *require* version 1.1 of a library, but *recommend* at least version 1.3.

## Versions

Versions should be specified in a format compatible with PHP's `version_compare()` function.

Examples:

- `2.3`
- `4.5.2`
- `1.2.0-alpha-3`

Version specifications MAY be accompanied by any of the following operators: `<`, `<=`, `>`, `>=`, `==`, `!=`. When versions are not accompanied by an operator, `>=` is assumed.

Examples:

- `"2.3"` — Anything greater than or equal to 2.3.0.
- `[">", "2.3.1"]` — Anything greater than 2.3.1.
- `[ [">", "2.3.1"], ["!=", "2.3.5"] ]` — Anything 2.3.1, but not 2.3.5.
- `[ [">=", "4.0"], ["<", "5.0"] ]` — Anything greater than or equal to 4.0.0, but less than 5.0.0.

## Syntax

``` json
{
    "required": {
        "wordpress": {
             "dot-org": {
                  "recommended": "3.9",
                  "required": "3.7"
             }
        },
        "plugins": { ... }
    }
}
```

In other words: `degree of dependency -> type of library -> library -> degree of dependency -> version`
