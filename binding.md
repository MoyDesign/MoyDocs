
## Bindings

A binding links web pages with parsers and templates. By looking into bindings the plugin detects pages for handling. A bindings consists of three properties: URL regex, parser and template. The latter two just indicate the parser and template to use for rendering. The former is a bit harder.

URL regex (or regexp) is a [regular expression](http://www.regular-expressions.info/) describing a page's URL. If a page's URL complies with the given regex, this binding is used for handling, i.e. the binding's parser and template is used to handle the page. 

The plugin applies regex **as is**, never modifying anything. This means for most cases you need to start your regex with `^` and, probably, finish it with `$`. Otherwise, it can match something from the middle of the URL, which is usually not what you want. 

The plugin applies regex to full URLs, including the schema (like `http://` or `https://`) and characters after `#`. This means for most cases you need to start your regex with `^http://` or `^https://`.

Do not forget about the special meaning of some characters in regexps. E.g. dot is a special character. This means to match `moy.design` you need to write `moy\.design`. 
