---
layout: post
title: "Manage package.json"
subtitle: "Javascript"
date: 2022-11-04
author: "Theo Cha"
tags:
  - Javascript
---

## What is package.json

package.json is a file that manages information about the current project and the dependencies of modules installed through the package manager (npm, yarn). It is usually located in the root directory.

It is a specification of the project and contains project and package information.

- Project information: name, version area
- Package version information: dependencies or devDependencies area. The package.json file must include name and version items.
- version: It must follow the format x.x.x, and the writing rule is called Semantic Versioning.

## Packages and Modules

1. Package
   package is a file or directory described as package.json.
   A package must have a package.json file to be published in the npm registry.

2. Module
   A module is a file or directory in the node_modules directory that can be loaded with the require() function of node.js.
   Modules do not need to have a package.json file. Not all modules are packages. Only modules with package.json are packages.

## Semantic Versioning

You may have seen dots and numbers representing versions. It is a software release version number naming system composed of [MAJOR.MINOR.PATCH].

When it was initially released, it starts with 1.0.0.

- MAJOR Version - (incompatible changes with previous versions) increment the first number by 1. If the MAJOR version is increased, the versions of MINOR and PATCH should be 0. ex) 2.0.0
- MINOR Version - (a new function compatible with the previous version) increases the middle number, and when the MINOR version goes up, the version of PATCH should be 0. ex) 1.1.0
- PATCH Version - (backward compatible bug fix) increases the 3rd number. ex) 1.0.1

![semantic_versioning](/img/post/semantic_versioning.png "semantic_versioning")

### Tilde(~) & Caret(^)

The version of the package contains ~, ^. ~ is called tilde and ^ is called caret.

Tilde (~)
This means that the PATCH level change of the package is allowed.
~17.0.18 has the same meaning as 17.0.18 or more and less than 17.1.0.
That is, it means that PATCH level change of less than 17.1.0 is allowed.

```
"devDependencies": {
"@types/node": "~17.0.18",
},
```

Caret(^)
This means that the MINOR and PATCH changes of the package are allowed.
^17.0.2 is equivalent to greater than or equal to 17.0.2 and less than 18.0.0.
That is, it means that MINOR and PATCH changes less than 18.0.0 are allowed.

```
"dependencies": {
"react": "^17.0.2",
}
```
