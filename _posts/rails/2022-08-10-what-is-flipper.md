---
layout: post
title: "What is Flipper"
subtitle: "Hello World, Hello Blog"
date: 2022-08-10
author: "Theo Cha"
tags:
    - Ruby on Rails
---


> “Feature flags”


![feature flags](/img/post/2022-08-10-what-is-flipper.png "Feature flags")

### What are feature flags?
----------------------------------------
Feature flags are a way to enable or disable a feature while the application is running. When the flag is enabled, the function is used. Otherwise, it is not. Imagine something like this in pseudocode.

### When to use feature Flag?
----------------------------------------
When a specific function is developed,
-	you want to make the function accessible only to specific personnel so that it can be tested in a production environment.
-	You want to enable or disable it at any time after deployment.

### Example scenario
----------------------------------------
Scenario: Over the past few days, we've developed a cool feature that everyone has been waiting for. It's big and complex, but it's been tested, so you can be sure everything is working. There is a deadline, so distribute it. Instantly all breaks.

Your functionality will break the entire app for some users. It's hard to say why. No bugs appeared during testing. Reverts the changes but does damage. The customer is not satisfied and will send it to do damage control and investigate what went wrong soon.

The problem is that releasing complex changes is inherently risky, no matter how much testing you do. It would have been nice to initially roll out this feature gradually to a small number of users. It would have been much better if I could have turned the feature off as soon as the first problem appeared.

### How to implement feature flag on Ruby on Rails?
----------------------------------------

[Flipper Gem](https://github.com/jnunemaker/flipper)

> “Beautiful, performant feature flags for Ruby.”

Use `Flipper#enabled?` in your app to check if a feature is enabled.

```cpp
# check if search is enabled
if Flipper.enabled? :search, current_user
  puts 'Search away!'
else
  puts 'No search for you!'
end
```

All features are disabled by default, so you'll need to explicitly enable them.

```cpp
# Enable a feature for everyone
Flipper.enable :search

# Enable a feature for a specific actor
Flipper.enable_actor :search, current_user

# Enable a feature for a group of actors
Flipper.enable_group :search, :admin

# Enable a feature for a percentage of actors
Flipper.enable_percentage_of_actors :search, 2
```

References
----------

- <https://github.com/jnunemaker/flipper>










