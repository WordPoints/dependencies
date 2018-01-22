# WSR-1

This document outlines the specifications for how dependents declare their dependencies.

This document builds on the prior information presented in [WSR-0](wsr-0.md).

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
- `integrates-with` - Things that aren't required, or necessarily needed for all sites, but which the extension will integrate with if present. 

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

Version specifications MAY be accompanied by any of the following operators: `<`, `<=`, `>`, `>=`, `!=`, `^`, `~`.

The meanings of `^` and `~` are similar to their meanings within [Composer](https://getcomposer.org/doc/articles/versions.md#next-significant-release-operators):

- `^` Indicates that the dependency follows [semantic versioning](http://semver.org/), and that the dependent is expected to be compatible with all versions of the library up to the next breaking version. Compatibility with the next breaking version is undefined.
- `~` Indicates that the dependent is expected to be compatible with all point releases, but compatibility with the next major or minor release is undefined.

Examples:

- `"2.3"` — Anything greater than or equal to 2.3.0.
- `[">", "2.3.1"]` — Anything greater than 2.3.1.
- `["^", "2.3.1"]` — Anything greater than or equal to 2.3.1, compatibility with 3.0.0+ is undefined.
- `["~", "2.3.1"]` — Anything greater than or equal to 2.3.1, compatibility with 2.4.0+ is undefined.
- `[ [">", "2.3.1"], ["!=", "2.3.5"] ]` — Anything greater than 2.3.1, but not 2.3.5.
- `[ [">=", "4.0"], ["<", "5.0"] ]` — Anything greater than or equal to 4.0, but less than 5.0.

> **Note on beta versions:** Beware that `[['>=', '1.3.0'], ['<', '2.0.0']]` would suggest compatibility with `2.0.0-beta-1`. However, this can be gotten around by instead specifying `['<', '2.0']` or `['<', '2.0.0-any']` instead. Better yet, use `^` instead of `>=`: `['^', '1.3.0']`.

## Relationships

Relationships are specified within this basic framework. However, the exact rules are specific to each dependency library type.

### `wordpress`

This relationship is intended to be specified by dependents that are WordPress extensions. Because of this, the relationship itself implies a requirement, since extensions by nature require WordPress. Thus, no relationship type needs to be specified, only the version info.

Since WordPress is usually fully backward compatible, only the `>=` operator should usually be used, and is the default.

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

### `wordpoints`

This relationship is intended to be specified by dependents that are WordPoints extensions. Because of this, the relationship itself implies a requirement, since extensions by nature require WordPoints. Thus, no relationship type needs to be specified, only the version info.

Because WordPoints follows semver, only the `^` version operator should be used, and is the default.

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

### `plugins`

For each plugin, the following information is needed:

- A slug that can be used to determine whether this plugin is installed (should correspond to the directory name of the plugin), and also to be used in regard to the update/install APIs.
- `name` — A human-friendly identifier for the plugin, that can be shown to the user when telling them that the plugin is not installed.
- `version` — The version info. All operators are allowed, `~` is the default.
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
            "url": "https://github.com/jrfnl/WP-adminbar-comments-to-pending/"
        }
    }
}
```

### `themes`

For each theme, the following information is needed:

- A slug that can be used to determine whether this theme is installed (should correspond to the directory name of the theme), and also to be used in regard to the update/install APIs.
- `name` — A human-friendly identifier for the theme, that can be shown to the user when telling them that the theme is not installed.
- `version` — The version info. All operators are allowed, `~` is the default.
- `url` (optional) - A URL for more info about this theme. May be used if the theme is not installed. Not necessary for themes from WordPress.org.

```json
{
    "themes": {
        "responsive": {
            "version": "1.1.0",
            "name": "Responsive"
        }
    }
}
```

```json
{
    "themes": {
        "responsive": {
            "version": { "required": "1.0.0", "recommended": "1.1.0" },
            "name": "Responsive"
        }
    }
}
```

```json
{
    "themes": {
        "my-example-theme": {
            "version": "1.0.0",
            "name": "My Example Theme",
            "url": "https://example.com/my-theme/"
        }
    }
}
```

### `wordpoints-extensions`

For each WordPoints extension, the following information is needed:

- A slug that may be used to determine whether this extension is installed (should correspond to the directory name of the extension).
- `name` — A human-friendly identifier for the extension, that can be shown to the user when telling them that the extension is not installed.
- `version` — The version info. Because extensions are required to follow semantic versioning, only the `^` operator should be used, and is the default.
- `server` — The server this extension is from.
- `ID` — The ID of this extension on the remote server.
- `url` (optional) - A URL for more info about this extension. May be used if the extension is not installed. Not necessary for extension from WordPoints.org.

```json
{
    "wordpoints-extensions": {
        "buddypress": {
            "version": "1.3.0",
            "name": "BuddyPress",
            "ID": "944",
            "server": "wordpoints.org"
        }
    }
}
```

```json
{
    "wordpoints-extensions": {
        "buddypress": {
            "version": { "required": "1.8.0", "recommended": "1.9.0" },
            "name": "BuddyPress",
            "ID": "944",
            "server": "wordpoints.org"
        }
    }
}
```

```json
{
    "wordpoints-extensions": {
        "example": {
            "version": "1.0.0",
            "name": "Example",
            "ID": "944",
            "server": "example.com",
            "url": "https://example.com/wordpoints-extension/"
        }
    }
}
```
