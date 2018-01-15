# PSR-1

This document outlines the specifications for how dependents declare their dependencies.

This document builds on the prior information presented in [PSR-0](psr-0.md).

## Definitions

- **Library Type:** What kind of library a dependency is. Examples: plugin, theme, WordPoints extension or component, WordPress, etc.
- **Relationship Type:** The kind of relationship between a dependent and a dependency.

## Overview

There are three attributes of every relationship between the dependent and a dependency:

- The Library Type of the dependency.
- The Relationship Type.
- The dependency versions targeted.

Each of these, in addition to the identifier for the dependency, needs to be made clear, or else the information is not useful.

## Library Types

- `wordpress` - Libraries implementing the WordPress API. WordPress core would be one, and any compatible fork would also be of this type.
- `plugins` - Libraries that are installed as WordPress plugins.
- `themes` - Libraries that are installed as WordPress themes.
- `wordpoints` - Libraries implementing the WordPoints API. WordPoints core would be one, and any compatible fork would also be of this type.
- `wordpoints-extensions` - Libraries that are installed as WordPoints extensions.

Additional types MAY be added.

## Relationship Types

There are different ways that a dependent library can relate to a "dependency", some of which don't actually imply *dependency* at all:

- `requires` - Things that are required for the extension to work. Without them, you will experience problems, maybe even the white screen of death.
- `integrates` - Things that aren't required, or necessarily needed for all sites, but which the extension will integrate with if present. 
- `incompatible` - Things with known incompatibilities. If the the extension is used along with them it will result in problems or even break the site.
- `supersedes` - Things that have been replaced by this extension. The extension is superior and should be used instead. If the extension is used along with them it will result in problems or even break the site.
- `superseded-by` - Things that have replaced this extension. They are superior, and should be used instead. If the extension is used along with them it will result in problems or even break the site.

Additional types MAY be added.

## Version Qualifiers

There are different ways that a dependent library can relate to a dependency version:

- `requires` - Versions that are required for the extension to work. Without them, you will experience problems, maybe even the white screen of death.
- `recommends` - Versions that are recommended to use with the extension. The extension will work without them, but some features may be crippled, though the site won't break entirely.

For example, a extension might *require* version 1.1 of a library, but *recommend* at least version 1.3.

Additional qualifiers MAY be added.

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
- `[ [">", "2.3.1"], ["!=", "2.3.5"] ]` — Anything greater than 2.3.1, but not 2.3.5.
- `[ [">=", "4.0"], ["<", "5.0"] ]` — Anything greater than or equal to 4.0, but less than 5.0.

## Relationships

Relationships are specified within this basic framework. However, the exact rules are specific to each dependency library type.

### `wordpress`

This relationship is intended to be specified by dependents that are WordPress extensions. Because of this, the relationship itself implies a requirement, since extensions by nature require WordPress. Thus, no relationship type needs to be specified, only the version info.

Examples:

```json
{
    "wordpress": "4.9.0"
}
```

```json
{
    "wordpresss": {
        "requires": "4.9.0",
        "recommends": "5.0.0"
    }
}
```

Since WordPress is usually fully backward compatible, only the `>=` operator should usually be used.

### `wordpoints`

This relationship is intended to be specified by dependents that are WordPoints extensions. Because of this, the relationship itself implies a requirement, since extensions by nature require WordPoints. Thus, no relationship type needs to be specified, only the version info.

Examples:

```json
{
    "wordpoints": "2.4.0"
}
```

```json
{
    "wordpoints": {
        "requires": "2.4.0",
        "recommends": "2.5.0"
    }
}
```

Because WordPoints follows semver, only the `>=` version operator may be used. The only exception is the use of the `<` operator for breaking versions. 

### `plugins`

For each plugin, the following information is needed:

- A slug that can be used to determine whether this plugin is installed (should correspond to the directory name of the plugin), and also to be used in regard to the update/install APIs.
- `name` — A human-friendly identifier for the plugin, that can be shown to the user when telling them that the plugin is not installed.
- `version` — The version info.
- `url` (optional) - A URL for more info about this plugin. May be used if the plugin is not installed. Not necessary for plugins from WordPress.org.

```json
{
    "plugins": {
        "buddypress": {
            "version": "1.8.0",
            "name": "BuddyPress"
        }
    }
}
```

```json
{
    "plugins": {
        "buddypress": {
            "version": { "required": "1.8.0", "recommended": "1.9.0" },
            "name": "BuddyPress"
        }
    }
}
```

```json
{
    "plugins": {
        "adminbar-link-comments-to-pending": {
            "version": "1.0.0",
            "name": "Adminbar Link Comments to Pending",
            "url": "https://github.com/jrfnl/WP-adminbar-comments-to-pending/",
        }
    }
}
```

### `themes`

### `wordpoints-extensions`
