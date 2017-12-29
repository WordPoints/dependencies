# PSR-0

This document outlines the scope and purpose of this project.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://www.ietf.org/rfc/rfc2119.txt).

## Definitions

- **Library**: A plugin, theme, WordPoints module, or other WordPress extension.
- **Dependency**: A library which another library integrates with.
- **Dependent**: A library which specifies other libraries as dependencies. 

## Overview

- Dependents MUST NOT cause errors when dependencies aren't installed or are the wrong version.
- This project SHALL set out specifications for dependents and SHALL NOT provide any specifications for dependencies.
- The purpose of this project is to provide a better user experience, and it SHALL NOT specify new APIs for developers except as required for that purpose.
- The scope of dependencies affected by this project are extensions of WordPress, and SHALL NOT include PHP, MySQL, or other high-level libraries.
- Libraries are visibly manifested to the user, and therefore depenency management MUST be transparent as well to avoid confusing users.

## Purpose

Many library developers have suggested that WordPress needs to provide a means of handling dependencies.[1](#Resources) These suggestions are being made by developers, who want to address problems that they encounter in their development workflow. These suggestions often include creating a new type of WordPress library that would act only as a dependency of other libraries.

These suggestions are usually met with opposition from the WordPress development team leaders, because the proposals are usually focused on addressing problems perceived by the developer, without due consideration for the experience of the user. These proposals might fix one developer snag, but they would introduce in the process more problems for developers and users alike.

Considering this, this project MUST NOT set out to solve a developer problem. Instead, this project's purpose is to identify and remedy problems experienced by the non-developer when using libraries which integrate with other libraries.

## Scope

The scope of this project SHALL NOT include server-level dependencies like PHP and MySQL versions. Users don't know what those are, and also have no control over them. Depdendents SHOULD always support the same set of versions for these high-level libraries as WordPress does. The scope of the project SHALL therefore be limited to WordPress and its extensions (plugins and themes).

The focus of this project SHALL be on the dependents, and SHALL NOT make any recommendations for dependencies. The problems that the user faces cannot ultimately be solved by the dependencies alone, without cooperation from the dependents on some level. While the dependencies can make the work of the dependents easier, this is done by implementing current best practices, and does not require dependencies to follow new recommendations beyond that. In addition, dependencies are libraries in their own right, and may have no way of knowing that they have depdendents. However, the developers of dependents know, by definition, about the depdencies, and can take steps to implement these recommendations. For this reason, the problems must be solved, to some degree, in coordination with the developers of dependents.

## General Problems

Before investigating specific problematic scenarios which the user might experience (in the [Problems](#Problems) section below), this section outlines some very general problems and their current mitigations based on the best practices recommended by WordPress development team leaders.

### White Screen of Death

#### Problem
The white screen of death is what you see when your site is broken by a fatal PHP error. This is something that should be avoided at all costs, because it is difficult for the user to rememdy. For this reason, dependents MUST NOT cause PHP errors when their dependencies aren't installed or are of the wrong version.

#### Mitigations
Avoiding fatal PHP errors MAY be done by checking for the existence of the dependency's functions and classes using `*_exists()` before using them. In many cases however, it is possible to avoid this issue by only using the dependency's functions within functions of the dependent that are only executed when called by actions or filters of the dependency. This later method SHOULD be used whenever possible.

### Broken Features

#### Problem
The features provided by a depedent or its dependency may be broken, even when the user has the dependency running, when the installed version of the dependency isn't supported by the installed version of the dependent. We could term this a dependency version mismatch. This problem is made worse when the user is running multiple dependents which require different versions of the shared dependency, resulting in a dependency conflict.

#### Mitigations
There are no significant mitigations to this problem, other than the dependent's developer promptly releasing compatibility updates whenever the dependency is updated. However, this is not always necessary for compatibility, and users who have not updated the dependency may find the dependent to be no longer compatible with their installed version of the dependency. Thus:

### Update Uncertainty

#### Problem
A user has a dependency and one or more dependents installed. We might refer to these collectively as a dependency web. When one of the members of a dependency web recieves an update, the user is often uncertain whether to install it, because compatibility with the other members of the dependency web is not explicit.

#### Mitigations
The dependent developer SHOULD take steps to mitigate this by explicitly stating the compatibility of the dependent with different versions of the depedency. However, as noted above, it is not always necessary (or even possible) for the dependent devleoper to release an update for compatibility with the dependency update. Releasing empty "compatibility" updates containing no code changes only exacerbates the problem and leads to update fatigue. At the same time, requiring the user to visit a dependent's web page to determine the compatiblity with the dependency also leads to update fatigue, because the user has to do more work for each update.

### Summary

These problems can be mitigated somewhat by following best practices. However, this only averts outright disaster, and does not actually provide a positive improvement in user experience. Combined, these issues work to create install/update fear in users—even when best practices are being followed.

## Problems

This section contains a list of problems identified in the current user experience, and outlines possible solutions.

Each of the problems is titled with a short summary of the scenario, followed by notes diveded into these sections:

- **Why this is a problem**: This section explains what the effect of this problem might be, and why this scenario is such a bad thing.
- **Minimum Solution**: This section gives a solution to the problem that would take the least possible work on the part of the developer—but might provide little improvement on the user's experience. This solution is probably already in use by dependents.
- **WWJD Solution**: This section gives the ideal, or the "God solution". We basically ask the question, "What Would Jesus Do?" If we were all-powerful and all-knowing and had no constraints, how might we solve this problem? Asking the question in this way helps us to define "ideal" in absolute terms, rather than relative to what we might normally think of as acheiveable.
- **Proposed Solution**: This section details the solution being proposed by this project. This will lie somewhere between the minimum solution and the WWJD solution. The goal is to find a solution as close to the latter as humanly possible.

### Problem 1: User installs dependent but the dependencies aren't installed.

#### Why this is a problem 
Users *will* actually do this. When the user activates the dependent, nothing will happen (ideally—in a worst case scenario they get the white screen of death).

#### Minimum Solution
The dependent shows a message when it is activated telling the user they need to install/activate its dependencies for it to work.

#### WWJD Solution
The dependencies are automatically installed and activated.

#### Proposed Solution
We should detect that the dependencies aren't installed, and when the depenent is activated give the user the option to install and activate them automatically.

We want to be transparent about this, because the user is installing something that is visible. That is, the dependency has forward-facing features, or, at the very least, will be visible in the list of installed plugins/themes. If the user sees these features (or the plugin/theme) and doesn't know where they have come from, this will cause confusion.

### Problem 2: User deactivates a dependency of an active dependent.

#### Why this is a problem
The user may not have realized that the dependent had this dependency, and may wonder why it has stopped working. In a worst-case scenario, they might suddenly get the white screen of death.

#### Minimum Solution
The dependent shows a message to the user when it detects a missing dependency.

#### WWJD Solution
The dependent is automatically deactivated, or the dependency remains active and the user is told why, depending on the user's intention.

#### Proposed Solution
The fact that the library is a dependency should be displayed to the user, and they shouldn't have the option to deactivate it alone. They should have to deactivate both the plugin depenency and all of its dependents.

### Problem 3: User updates a dependency to a version not compatible with a dependent.

#### Why this is a problem
The dependent may stop working, or be crippled, or the user might get the white screen of death.

#### Minimum Solution
This is actually non-trivial for the dependent itself to handle. It probably won't know about the incompatibilities in most cases. It can use `*_exists()` calls to avoid total disaster, though.

#### WWJD Solution
The incompatibilities are instantaneously resolved.

#### Proposed Solution
There are two parts to this. We first need to be able to determine whether there are incompatibilities. Second, we need to find out how/if we can resolve them. 

We can take a two-pronged approach to compatibility checking:
- First, it would be nice to have a way to check if there were any known incompatibilities before the update is even performed. This could involve an API endpoint on the library's distributor. We could also check the local dependencies definition (assuming it exists). 
- Second, we could actually do a dry-run of the update and see if things explode. This will only detect huge breaks (things that the dependent can already partly mitigate via `*_exists()` checks as noted above). However, maybe we could go beyond that. Think really big and sophisticated. Maybe we could actually run a suite of tests that the dependent offers, and if those fail we know that there is an incompatibility.

Resolving the incompatibility could be as simple as checking for an update for the dependent, and offering to install it if one exists. Other than that, all we can do is tell the user that if they want to update they'll need to deactivate the dependent or risk breaking their site.

Notes:
- Running dry-run updates with automated tests actually looks like the future. This is as close as you can humanly get to nothing-breaks updates. While it might take a lot of work to implement at first, I think it would be really worth it. We can start with simple dry-runs and work on the testing part as we go along.

### Problem 4: User updates a dependent which no longer supports the installed version of a dependency.

#### Why this is a problem
Things might break, causing concern, frustration, injury or even (the white screen of) death.

#### Minimum Solution
Show the user a message telling them that the installed version of the dependency is no longer supported and they need to update.

#### WWJD Solution
Automatically upgrade the dependency, or don't upgrade the dependent, depending on the user's intention. Or, automatically resolve any conflicts so that they both just work together.

#### Proposed Solution
When the user updates the dependent, give them the option to update the dependency as well. This would require that we already know about the incompatibility via an API endpoint provided by the library distributor, or another means. Dry-run checks as noted in problem 3 could still be useful.

### Problem 5: User installs libraries with conflicting dependencies

#### Why this is a problem
One or both of the dependents may break, or cease to function fully.

#### Minimum Solution
Dependents show a message telling the user that the dependency is the wrong version. (This will probably just confuse and frustrate the user in many cases.)

#### WWJD Solution
The conflict is immediately resolved.

#### Proposed Solution
We need to know whether we can resolve the conflict, and if so how the user would want it to be done.

We can determine whether the conflict can be resolved by looking at each library's dependencies and determining whether a happy medium exists. We can also check whether there are updates available for any of the libraries involved, and see if they will help resolve the conflict.

Notes:
- Most importantly, by solving the previous problems, we will actually prevent a library with a dependency conflict from ever being installed on the site. That doesn't actually solve the conflict itself though.
- We should be smart about this when libraries are being installed/updated, so that we don't just tell the user not to install the library if the conflict could be resolved. We also don't want to tell them to update/install a library when it will cause a dependency conflict.
