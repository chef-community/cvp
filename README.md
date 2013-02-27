# Cookbook Versioning Policy v0.1.0

## Introduction

A [Chef](http://www.opscode.com/chef/) cookbook is identified by the combination of its name and version, both of which are defined in `metadata.rb`. After a cookbook has been initially developed and put into use, any changes should be accompanied by an appropriate change to its version when those changes are published. This will allow a correctly working version to be specified with a version constraint in a run_list or environment. The purpose of this document it to define an "appropriate change" to the cookbook's version.


## Semantic Versioning

This policy applies the [Semantic Versioning Specification](http://semver.org/) to cookbooks. Semantic versioning requires an understanding of the public API of a cookbook, and of what constitutes a backward compatible and non-backward compatible change to that API. This document attempts to provide that understanding.


## Public API

The public API of a cookbook consists of anything which, if changed, could break a reasonable use[1](#1) of that cookbook. Sometimes this is a judgement call, but it will always include changes to the following:

- An attribute's name or its cookbook-assigned value
- A recipe name
- A resource name
- The name, allowed values, or default value of a resource's attributes
- A change to the public API of a library file. This includes things like name changes to visible classes, modules, methods, or variables, and changes to a method's signature.
- A definition's name or parameters

Most aspects of a cookbook's behavior are also a part of its public API. This is an area where judgement is required; things like bug fixes are not usually considered API changes.


## Cookbook Versions

A cookbook's version consists of major, minor, and patch versions. Any change to the cookbook requires a new version when that change is published. The nature of the change determines the new version:

- A non-backward compatible change to the cookbook's public API requires incrementing the cookbook's major version number
- A backward compatible change to the cookbook's public API requires incrementing the cookbook's minor version number
- A change that does not affect the cookbook's public API requires incrementing the cookbook's patch version number. Examples would include bug fixes, documentation updates, or performance enhancements.

As described in SemVer, when the major or minor version number is incremented the less significant parts of the version should be reset to zero.

For the remainder of this document we will refer to non-backward compatible changes to a cookbook's public API as "major," backward compatible changes to a cookbook's public API as “minor,” and changes with no effect on the public API as "patch."


## Changes and their Effect on Version Numbers

The following sections categorize the impact of changes to parts of a cookbook (attributes, recipes, etc.) as major or minor. Keep in mind the following principles:

- Any change to an in-use cookbook, when published, requires a version number change. If the public API is unaffected the patch version number must still be incremented.
- If a change isn't listed below, you must still analyze its likely impact on the existing uses of the cookbook and make an appropriate version number change.
- A cookbook's behavior is an important part of its public API, but it is difficult to categorize behavioral changes with hard and fast rules. Use your judgement.

### Changes to Attributes

#### Major Changes to Attributes

- A change to the name of an attribute used by the cookbook
- A change to the value assigned to an attribute by the cookbook
- Removal of an attribute used by the cookbook
- Addition of an attribute used by the cookbook, when the cookbook does not provide a default value (such a change should be avoided by assigning a sensible default value)

#### Minor Changes to Attributes

- The addition of a new attribute to the cookbook, when the cookbook provides it a sensible default value

### Recipes

#### Major Changes to Recipes

- A change to a recipe's name
- Removal of a recipe
- A change to behavior of a recipe that may change existing functionality

#### Minor Changes to Recipes

- Addition of a new recipe
- Additions to a recipe that add new features or behavior, but do not break existing functionality

### Files and Templates

Cookbook files and templates are not part of the public API of a cookbook, unless they are intended to be used from outside the cookbook. So examined in isolation, a change to a cookbook file or template would usually constitute a patch change. However, these changes are often accompanied by changes to the recipe that declares the cookbook_file or template resource, or by changes to attributes used in the template. Examine these changes for potential major or minor version impacts.

### Resources and Providers

#### Major Changes to Resources and Providers

- A change to the name of a resource or provider
- Removal of a resource or provider
- A change to the name of a resource's attribute
- A change to the valid types of a resource's attribute, unless the change widens those types
- Addition of a new resource attribute, if it is required and does not have a default value. A change of this type should be avoided; instead, provide a sensible default value to make this a minor change.
- Changing an existing resource attribute from not required to required, or removing the default value from a required attribute
- Removal of a resource's attribute

#### Minor Changes to Resources and Providers

- Addition of a new resource or provider
- Addition of a new attribute to a resource, if it is not required or has a default value
- A widening of the valid type(s) of a resource's attribute
- The addition of a default value to an existing resource attribute
- Changing an existing resource attribute from required to not required

### Libraries

Libraries contain arbitrary Ruby code, so changes to libraries are governed by the same rules of public API compatibility as any other Ruby code. In this section the term “publicly accessible component” refers to any module, class, method, or variable accessible from outside the cookbook. [2](#2)

#### Major Changes to Libraries

- Changing the name of a publicly accessible component of the library
- Removing a publicly accessible component of the library
- Changing the signature of a publicly accessible method in a non-backward compatible way

#### Minor Changes to Libraries

- Adding a new publicly accessible component to the library
- Changing the signature of a publicly accessible method in a backward compatible way, such as:
  - Appending a new parameter with a default value
  - Widening the valid types for a parameter
  - Narrowing the method's return types

### Definitions

#### Major Changes to Definitions

- Changing the name of a definition
- Removing a definition
- Adding a parameter, unless its default value is suitable for existing uses of the definition
- Changing the name of a parameter
- Removing a parameter

#### Minor Changes to Definitions

- Adding a definition
- Adding a parameter to a definition, if its default value is suitable for existing uses of the definition

## Miscellaneous Major Version Changes

- Narrowing the range of Ruby or Chef versions supported by the cookbook
- Adding a dependency on code not included in the Ruby core or in Chef

## Miscellaneous Minor Version Changes

- Widening the range of Ruby or Chef versions supported by the cookbook


---
### Notes:

<a id="1">1. The "reasonable use" restriction is intended to exclude things like [chef-rewind](https://github.com/bryanwb/chef-rewind), which effectively turns the entire cookbook into a public API.</a>

<a id="2">2. Ruby does little to enforce separation of API from implementation. As a result, the determination of a library's API becomes a matter of intent. For example, a private method defined in a library class can be called from outside the cookbook by reopening the class. But by defining the method as private, the cookbook intended it not to be part of the API, so a change to the method would only require a patch change to the cookbook's version.</a>