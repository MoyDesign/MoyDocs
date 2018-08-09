
| Prev | Moy.Design Documentation          | Next                        |
| ---- |:---------------------------------:| --------------------------- |
|      | [Contents](../README.md#contents) | [Browser plugin](plugin.md#browser-plugin) |
---

# Overview

**Moy.Design**, or **Moy** for short, allows you to change the appearance of your favorite websites. To make it happen, you need three things:

1. A [parser](parser.md#parser) is an instruction how to extract information from websites;
2. A [template](template.md#template) is an instruction how to show information;
3. The [browser plugin](plugin.md#browser-plugin), which uses parsers and templates to show information from websites the way you want.

The general flow is like this:

![General flow](general-flow.svg)

When you're opening a website, the plugin checks whether there is a parser for it. If yes, the plugin prevents normal loading, and loads only the data necessary for the parser. Typically, it's only the main page and images, without scripts, stylesheets, fonts, etc. The page typically loads considerably faster because less data is transferred.

Next, the plugin extracts important information from the page according to the parser rules. The result of this operation is a set of named HTML strings.

Then, the plugin renders the template with the parsed data. The result of this operation is a new HTML page.

Finally, the plugin substitutes the original HTML page with the new one. So, you see the website in your own way.

> Parsers and templates are separate entities. They don't depend on each other. They may (and should) be written and used separately. For instance, you can use one template with many parsers.
---
| Prev | Moy.Design Documentation          | Next                        |
| ---- |:---------------------------------:| --------------------------- |
|      | [Contents](../README.md#contents) | [Browser plugin](plugin.md#browser-plugin) |
