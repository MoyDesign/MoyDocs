
| Prev | Moy.Design Documentation          | Next                        |
| ---- |:---------------------------------:| --------------------------- |
| [Overview](overview.md#overview) | [Contents](../README.md#contents) | [Parser](parser.md#parser) |
---

# Browser plugin

1. [Overview](#overview)
1. [Contributions](#contributions)

## Overview

Websites data is never sent anywhere. **Parsing and templating are done on the client (in your browser).** This means you can use Moy even for websites with sensitive data. This also means you need to install a browser plugin.

The plugin's available for Chrome and Firefox (including Firefox for Android).

* [Install for Chrome](https://chrome.google.com/webstore/detail/moydesign/kgepfphemgiidklhpnfoobmoieiglgon)
* [Install for Firefox](https://moy.design/extension/firefox)

If everything's OK, you should see something like this next time you click the plugin's icon:

<img src="plugin-popup.png" height="300">

These buttons represent alternative looks (templates) available for this page. For some pages, only `Original look` is available. When you press a button, the page will be reloaded with the chosen look, and your choice is remembered. I.e. next time you load this (or similar) page, it'll be shown with the selected template.

## Contributions

The plugin's own code is located in the [MoyPlugin](https://github.com/MoyDesign/MoyPlugin) repository. Please feel free to file issues and provide pull requests.

The plugin loads parsers and templates from the [MoyData](https://github.com/MoyDesign/MoyData) repository. Please feel free to file issues and provide pull requests.

---
| Prev | Moy.Design Documentation          | Next                        |
| ---- |:---------------------------------:| --------------------------- |
| [Overview](overview.md#overview) | [Contents](../README.md#contents) | [Parser](parser.md#parser) |
