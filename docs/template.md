| Prev | Moy.Design Documentation          | Next                        |
| ---- |:---------------------------------:| --------------------------- |
| [Parser](parser.md#parser) | [Contents](../README.md#contents) |    |
---

# Template

1. [Overview](#overview)
1. [Information block](#information-block)
1. [Template types](#template-types)
1. [Template content](#template-content)
1. [Examples and contributions](#examples-and-contributions)

## Overview

A template is an instruction on how to show the information extracted by parsers. Templates are not tightly bound to parsers, but rather represent the collection of new looks to choose from. Different parsers may be used with the same template, and vice versa.

Internally, template is an HTML document with [Handlebars](http://handlebarsjs.com/) tags. You don't need and should not include any Handlebars scripts or compile templates manually. This is all done automatically.

## Information block

Template must start with the Handlebars comment with the information block:
```handlebars
{{!--
name: Gray low-contrast reader
type: article
author: Moy.Design
description: Minimalistic low-contrast template with gray background.
  Displays a single blog post in a human-friendly way without needless formatting.
--}}
```
The information block is a YAML text contains the following fields:

1. `name`: string, the template's name. Must be unique and match the parser's file name (without the `.handlebars` suffix).
1. `description` *(optional)*: string, human-readable template's description.
1. `author` *(optional)*: either string or object. If it's an object, it must contain the `name` field. Also, authors amy include additional information about themselves via additional fields (such as `email`).
1. `type`: string, template type. Possible values so far: `article`, `feed`, `custom`. See about types [below](#template-types).
1. `customType` *(optional)*: string, must be present if the type is `custom` (and must not be present otherwise). See about types [below](#template-types).

## Template types

The plugin uses types to match parsers with templates. For now, the following types are recognized:

1. `article`: a single article or blog post (possibly) with comments.
1. `feed`: a feed or list of articles or blog posts.

The number will grow in the future.

Parser and template writers can use their own types (via `customType`), if the standard types are not sufficient.

## Template content

Templates can use the following variables (tokens, names):

* `BASE_URL`: the original URL of the page;
* `TITLE`: the content of the `<title>` tag in the original page;
* `ICON_TAGS`: the whole `<link rel="icon" ...>` or `<link rel="shortcut icon" ...>` tag(s);
* `OPEN_GRAPH_TAGS`: all [Open Graph](http://ogp.me/) metadata tags found in the original document (e.g. `<meta property="og:title" content="Title">`, etc);
* **any name parser provided.**

Parsers provide *HTML strings.* This means if you don't want them to be escaped you need to use triple curly bracers, i.e. `{{{name}}}` instead of `{{name}}`.

Parsers always return lists, but very often they contain just one element. In this case, you can just call them as if they were simple values: `{{{name}}}`. If they contain multiple values, you can use the `each` helper:
```handlebars
{{#each name}}
    <p>{{{this}}}</p>
{{/each}}
```
This also works for the rules with sub-rules:
```handlebars
{{#each article}}
    <h2>{{{title}}}</h2>
    <p>{{{body}}}</p>
{{/each}}
```
See the Handlebars [docs](http://handlebarsjs.com/builtin_helpers.html) for details.

We encourage template creators to always use automatically provided names. This will allow Moy to generate a more polished Web pages. Typical template skeleton may look like this:
```handlebars
{{!--
name: Your template name
type: article
author: Your name
description: Your template description
--}}
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <base href="{{ BASE_URL }}">
  <title>{{ TITLE }}</title>
  {{{ ICON_TAGS }}}
  {{{ OPEN_GRAPH_TAGS }}}
  <!-- ... other head tags here, if needed ... -->
  <style>
    <!-- ... template styles here ... -->
  </style>
</head>
<body>
  <!-- document structure -->
</body>
</html>
```    
## Examples and contributions

The plugin loads templates from the [MoyTemplates](https://github.com/MoyDesign/MoyData/tree/master/MoyTemplates) directory of the [MoyData](https://github.com/MoyDesign/MoyData) repository. There you can find a lot of template examples.

**Contributions are welcome!** Feel free to file issues and provide pull requests with template fixes and new templates.

---
| Prev | Moy.Design Documentation          | Next                        |
| ---- |:---------------------------------:| --------------------------- |
| [Parser](parser.md#parser) | [Contents](../README.md#contents) |    |
