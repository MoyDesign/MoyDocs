
| Prev | Moy.Design Documentation          | Next                        |
| ---- |:---------------------------------:| --------------------------- |
| [Browser plugin](plugin.md#browser-plugin) | [Contents](../README.md#contents) | [Template](template.md#template) |
---
# Parser

1. [Overview](#overview)
1. [Information block](#information-block)
1. [Parser types](#parser-types)
1. [Rules block](#rules-block)
    1. [rewrite](#rewrite)
1. [Redirect block](#redirect-block)
1. [Examples and contributions](#examples-and-contributions)

## Overview

A parser is an instruction for the plugin on how to extract information from web pages. It is a [YAML](http://yaml.org/) document of the following format:

    info:
      name: AdMe article
      description: Parses a single AdMe.ru article.
      author: Moy.Design
      type: article
      domain: adme.ru
      path: '/[^/]+/[^/]+.*'
      testPages: 'https://moy.design/example/lorem-ipsum.html'

    rules:
        - name: favicon
          value: "https://moy.design/favicon.ico"
        
        - name: global_headline
          match: .headline
        
        - name: post
          match: article
          rules:
              - name: level
                attribute: class
                rewrite:
                  find: 'b-tree-twig-(\d+)'
                  output: '$1'
                    
              - name: author
                match: .entryunit__author
                
              - name: author_link
                match: 
                    include: a.entryunit__author__link
                    attribute: href
                
              - name: title
                match:
                    include: .entry-title
                    exclude: .entry-linkbar
                    outerNode: true
                    removeInside: .unwanted-class
                
              - name: body
                match: 
                  include: .entryunit__text
                  keepBasicMarkup: true
                
              - name: another_body
                match:
                  include: dt
                  exclude: .banner
                  addNextUntil: "dt, .banner"
              
              - name: date
                match: 
                    or:
                      - .vcard .updated
                      - .asset-entry-date .datetime
                      - .b-singlepost-author-userinfo-screen .b-singlepost-author-date

    redirect:
        query:
            setParams:
                a: 1
                b: abcd

## Information block

A parser **must** contain the `info` block at the top level. Its value is an object with the following keys.

1. `name`: string, the parser's name. Must be unique and match the parser's file name (without the `.yaml` suffix).
1. `description` *(optional)*: string, human-readable parsers description.
1. `author` *(optional)*: either string or object. If it's an object, it must contain the `name` field. Also, authors amy include additional information about themselves via additional fields (such as `email`).
1. `type`: string, parser type. Possible values so far: `article`, `feed`, `custom`. See about types [below](#parser-types).
1. `customType` *(optional)*: string, must be present if the type is `custom` (and must not be present otherwise). See about types [below](#parser-types).
1. `domain`: string, the name of the domain the parsers works with. Without prefixes like `http://` and suffixes.
1. `path` *(optional)*: string, regular expression used to determine if the page's URL's path (everything which goes *after* the domain) matches the given parser, i.e. if the parser can be used to parse this page. Either `path` or `suggestedRegex` (see below) must be given. If `path` is given instead of `suggestedRegex` the plugin will generate the full page regex automatically.
1. `suggestedRegex` *(optional)*: string, regular expression used to determine if the page's full URL (with prefixes like `http://`, domain, hashes and everything else) matches the given parser, i.e. if the parser can be used to parse this page. Either `path` or `suggestedRegex` must be given.
1. `testPages` *(optional)*: string or array of strings, full URL(s) of page(s) this parser can be tested on.

## Parser types

The plugin uses types to match parsers with templates. For now, the following types are recognized:

1. `article`: a single article or blog post (possibly) with comments.
1. `feed`: a feed or list of articles or blog posts.

The number will grow in the future.

Parser and template writers can use their own types (via `customType`), if the standard types are not sufficient.

## Rules block

A parser **must** contain the `rules` block at the top level. Its value must be a list of rule definitions.

A rule definition is an object with two must-have fields: `name` and one of `match`, `value` or `attribute`. There can also be an optional `rewrite` field (see below).

`name` contains the rule's name. It must be unique. By that name the rule will be referenced in templates. All-caps names (with or without underscores) like `BASE_URL` are reserved for special use and should not appear in parsers. Encouraged naming style is `lowercases_with_underscores`, but this is not required. We recommend to avoid plural forms in names, even if you expect a list of objects. E.g. use `article` instead of `articles`. This way [templates](#template) will look nicer.

`value` is just a string (or array of strings), the constant value assigned for this rule.

`attribute` is a string denoting an attribute name (such as `href` for links). In this context, it means getting the attribute of the parent node with the given name. Other usages are possible (see below).

`match` may be the following:

1. A string, in which case it must be a valid [jQuery selector string](https://api.jquery.com/category/selectors/). Such a rule matches all HTML nodes selected by the given selector.
2. An object with the `include` and (possibly) `exclude` (both must be valid jQuery selectors) and some other fields (see below). Such a rule matches all HTML nodes selected by `include`, which are **not** comply with `exclude`. Additional fields may be specified here:
  * `removeInside`: this field (if present) must be either a valid jQuery selector or an array of them. Everything matched by them is removed from the parsed tags. The diffirence between `exclude` and `removeInside` is that the former specifies an exclusion from the `include` selector, while the latter specifies, what must be removed from the insides of tags already matched by the `include`/`exclude` combination.
  * `attribute`: must be a string denoting an attribute name (such as `href` for links). If specified, for this rule the parser returns contents of the attribute instead of contents of the tag itself. `removeInside`, `outerNode` and `keepBasicMarkup` don't make sense with this field and will be ignored.
  * `outerNode`: if it's `true` or `1`, the result will be the full node with its outer markup. If it's `false` or `0` (which is the default), the result will be its inner content. This field is ignored, if `addNextUntil` is specified.
    > <small>Please, avoid this field as much as possible, since it makes writing good looking templates hard.</small>
  * `keepBasicMarkup`: if it's `true` or `1`, **basic** HTML tags inside of the matched tag are preserved. Otherwise, all markup is removed, leaving only the text (this is the default). If `outerNode` is `true`/`1` or there are sub-rules, this field is assumed to be `true` as well, even if specified as `false`. Basic HTML tags include `b`, `i`, `div`, `a`, etc. Their styles and CSS classes are stripped.
    > <small>Excessive use of this field makes writing good looking templates hard. We recommend to only use it for things like an article body, where arbitrary markup is normal.</small>
  * `addNextUntil`: if present, this must be a valid JQuery selector. In this case, each node matched is supplemented with its next siblings until a sibling matching this selector is found (or siblings ended). If this is specified, `outerNode` field is ignored.
3. An object with the `or` field. This field must contain the list of the first two cases (may be mixed). Such a rule matches the same as **the first** non-empty match from the given list.

Optionally, rule definition may contain the `rules` block itself. It works the same as the top level one, but all sub-rules are only searched inside of the parent rule, not globally. So, in the example above, the rule `author` will match all HTML nodes with the `entryunit__author` class **inside of** that `<article>` tag, that was matched by its parent rule (`post`). 

The `rules` block may be nested multiple times. But be reasonable with the depth. 

We encourage you to write parsers so they return collections of meaningful objects with structure, like the collection of `article`s with fields `title`, `body`, `author`, etc. But sometimes the original markup is not structured. Consider the following example:

    <dl>
        <dt>Article 1 title</dt>
        <dd>Article 1 body</dd>
        <dt>Article 2 title</dt>
        <dd>Article 2 body</dd>
        ...
    </dl>

`addNextUntil` helps with parsing such cases. For this example, we could make a parser like this:

    rules:
      - name: article
        match:
          include: dt
          addNextUntil: dt
        rules:
          - name: title
            match: dt
          - name: body
            match: dd

The `article` rule matches all `dt` tags and supplements them with whatever siblings go after them, until the next `dt` tag is found. The subrules then parse titles and bodies from the combined objects.

### rewrite

`rewrite` is an optional field in the rule definition. It allows to change the parsed content. It's not allowed for rules with subrules (i.e. with a nested `rule` block). 

    rewrite:
      find: 'b-tree-twig-(\d+)'
      output: '$1'

`rewrite` block has one required field (`output`) and one optional (`find`).

1. `find` specifies a regular expression. It may have groups, which can be referenced in `output`. If not specified, it's set to `.*`. The regular expression is applied *only once*. Note, that single quotes is used. In single quotes, `\` is not treated as escape character in Yaml. If we used double quotes (`"`), we'd need to double the slash.
2. `output` specifies a new value for token. It's a string which may contain references `$1`, `$2`, etc. `$0` means the whole text parsed by `find`.

If a `rewrite` block is given for a rule, first the rule parses the source normally. Then *each token* parsed by the rule is parsed by the `find` regexp. Then the token is *substituted* by the `output` text with references to parsed groups resolved accordingly.

For example, if a rule parsed a token `something b-tree-twig-8 anything else`, the rewrite block above would transform it to just `8`.

## Redirect block

There can also be an (optional) `redirect` top-level block. It allows to modify the original document's URL. If it is present, the plugin will redirect the browser to the modified URL before parsing.

For now, only query parameters modification is supported. You can add/modify query parameters (the stuff which goes after `?`) like this:

    redirect:
        query:
            setParams:
                q: moy
                show: all

This tells Moy to add parameters `q` and `show` with values `moy` and `all` respectively. If the original URL contains parameter(s) with the same names, their values will be overwritten. Let's say, the original URL was `https://moy.design/?q=abcd`. The modified URL (to which the user gets redirected) will be `https://moy.design/?q=moy&show=all` (or `https://moy.design/?show=all&q=moy`, the order is not guaranteed).

## Examples and contributions

The plugin loads parsers from the [MoyParsers](https://github.com/MoyDesign/MoyData/tree/master/MoyParsers) directory of the [MoyData](https://github.com/MoyDesign/MoyData) repository. There you can find a lot of parser examples.

**Contributions are welcome!** Feel free to file issues and provide pull requests with parser fixes and new parsers.

---
| Prev | Moy.Design Documentation          | Next                        |
| ---- |:---------------------------------:| --------------------------- |
| [Browser plugin](plugin.md#browser-plugin) | [Contents](../README.md#contents) | [Template](template.md#template) |
