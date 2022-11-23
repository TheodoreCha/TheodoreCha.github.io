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

### npm update

#### `npm update --save`

This command will update all the packages listed to the latest version

#### `npm ci`

The main differences between using npm install and npm ci are:

The project must have an existing package-lock.json or npm-shrinkwrap.json.

- If dependencies in the package lock do not match those in package.json, npm ci will exit with an error, instead of updating the package lock. npm ci can only install entire projects at a time: individual dependencies cannot be added with this command.
- If a node_modules is already present, it will be automatically removed before npm ci begins its install.
- It will never write to package.json or any of the package-locks: installs are essentially frozen.

#### `npm outdated`

With this command, you can list all dependencies supposed to update. it is a useful command when you want to manually update a dependency to debug. With this command, you can list all dependencies supposed to update. it is a useful command when you want to manually update a dependency to debug. also, the console messages show some dependencies are red and some of them are yellow. the red means that `npm update` will update those dependencies but the yellow means `npm update` will not update so it requires a manual update.

```js
Package                            Current   Wanted   Latest  Location                                        Depended by
@commitlint/cli                     17.2.0   17.3.0   17.3.0  node_modules/@commitlint/cli                    component-library
@commitlint/config-conventional     17.2.0   17.3.0   17.3.0  node_modules/@commitlint/config-conventional    component-library
@rollup/plugin-babel                 5.3.1    5.3.1    6.0.2  node_modules/@rollup/plugin-babel               component-library
@rollup/plugin-commonjs             22.0.2   22.0.2   23.0.2  node_modules/@rollup/plugin-commonjs            component-library
@rollup/plugin-node-resolve         14.1.0   14.1.0   15.0.1  node_modules/@rollup/plugin-node-resolve        component-library
@rollup/plugin-typescript            8.3.3    8.3.3    9.0.2  node_modules/@rollup/plugin-typescript          component-library
@testing-library/react              12.1.5   12.1.5   13.4.0  node_modules/@testing-library/react             component-library
@typescript-eslint/eslint-plugin    5.43.0   5.44.0   5.44.0  node_modules/@typescript-eslint/eslint-plugin   component-library
@typescript-eslint/parser           5.43.0   5.44.0   5.44.0  node_modules/@typescript-eslint/parser          component-library
babel-loader                         8.3.0    8.3.0    9.1.0  node_modules/babel-loader                       component-library
chalk                                4.1.2    4.1.2    5.1.2  node_modules/chalk                              component-library
eslint                              8.27.0   8.28.0   8.28.0  node_modules/eslint                             component-library
```

- If you want to keep the previous version, you can remove the Caret(^) on `package.json` as:

```json
"@example-dependency": "^7.19.3", //from
"@example-dependency": "7.19.3",  //to
```

- If you want to update major update, you can copy the latest version on the list from `npm outdated` and update them manually

```js
      current   Wanted   Latest // From the `npm outdated`
react  17.0.2   17.0.2   18.2.0  node_modules/react

"react": "17.0.2", //from
"react": "18.2.0", //copy from the latest
```
