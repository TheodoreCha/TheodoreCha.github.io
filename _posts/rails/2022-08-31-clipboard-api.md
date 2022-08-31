---
layout: post
title: "How to work with Javascript Clipboard API"
subtitle: "Javascript"
date: 2022-08-31
author: "Theo Cha"
tags:
  - Javascript
  - Jest
---

## Overview

> Clipboard API
> I have got a feature request that users can copy a web URL address by clicking a button. this way, users simply can copy and paste any given text.

## How?

### 1. document.execCommand()

document.execCommand() has been a common way to build the feature but it is deprecated:

[The official doc says](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand)
Deprecated: This feature is no longer recommended. Though some browsers might still support it, it may have already been removed from the relevant web standards, maybe in the process of being dropped, or may only be kept for compatibility purposes. Avoid using it, and update existing code if possible; see the compatibility table at the bottom of this page to guide your decision. Be aware that this feature may cease to work at any time.

---

### 2. Clipboard API

This API is designed to supersede accessing the clipboard using a document.execCommand() and this API had four methods (Clipboard.read(), Clipboard write(), Clipboard.readText(), Clipboard.writeText()). [Official Documentation](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard)

At the beginning of the implementation, I had a couple of questions from the docs:

---

#### What methods should I use?

There are four methods and it took some time to fully understand what I should use. it is `Clipboard.writeText()`.

---

#### How do its permissions work?

> Calls to the methods of the Clipboard object will not succeed if the user hasn't granted the needed permissions using the Permissions API and the 'clipboard-read' or ‘clipboard-write’ permission as appropriate.

I thought we need to always ask for permission to use this new API feature but the permissions are only for `Clipboard.read()`, `Clipboard.readText()`. Basically only for reading methods.

> The "clipboard-read" permission of the Permissions API must be granted before you can read data from the clipboard.
> For the Clipboard.writeText() and write() we do not need to get a permit because:

For the Clipboard.writeText() and write() we do not need to get a permit because The "clipboard-write" permission of the Permissions API, is granted automatically to pages when they are in the active tab.

> The "clipboard-write" permission of the Permissions API, is granted automatically to pages when they are in the active tab.

---

#### How to test in a development env if the API only works in secure contexts(HTTPS)?

> Secure context: This feature is available only in secure contexts (HTTPS), in some or all supporting browsers. - https://developer.mozilla.org/en-US/docs/Web/API/Clipboard

We can config our browser to enable API with the following instruction:

1. Type chrome://flags/#unsafely-treat-insecure-origin-as-secure in the address bar.
2. Add the origin which you need to treat as secure as shown in the below image.
3. Relaunch chrome.

   [Go to the detail](https://stackoverflow.com/questions/40696280/unsafely-treat-insecure-origin-as-secure-flag-is-not-working-on-chrome)

---

#### Code examples

```cpp
function clipBoard(e) {
 return navigator.clipboard.writeText(e);
 }
```

```cpp
  anySelector.addEventListener('click', async () => {
    try {
      await clipBoard(inputQuerySelector.value);
    } catch (error) {
      console.error('Error!: ', error);
    }
  });
```

## References

- <https://web.dev/async-clipboard/>
- <https://stackoverflow.com/questions/40696280/unsafely-treat-insecure-origin-as-secure-flag-is-not-working-on-chrome>
- <https://stackoverflow.com/questions/61348091/why-is-this-mock-jest-test-failing>
- <https://web.dev/async-clipboard/>
