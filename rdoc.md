---
title: Rdoc
category: Markup
layout: 2017/sheet
---

### Basic RDoc format

```rb
# Foo.
#
# @example
#
#   y
#   g
#
# @param [String] param_name The xx and xx.
#
<<<<<<< HEAD
# @see http://url.com
=======
# @see https://example.com/
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
#
# @return [true] if so
```

### Inline

```markdown
*bold*
_emphasized_
+code+
```

```markdown
<<<<<<< HEAD
http://www.link.com
See Models::User@Examples
{Google}[http://google.com]
=======
https://www.example.com/
See Models::User@Examples
{Google}[https://google.com/]
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
```

### Skip

```rb
def input # :nodoc:
```

```rb
module MyModule # :nodoc: all
```

### Definition lists

```rb
# == Definition lists
#
# list::  hi.
# +foo+:: parameterized
```

```rb
# == Definition lists
# [foo]   also
# [bar]   like this
```

### Return types

```rb
# @return [String]
# @return [String, nil] the name
```

### Callseq

```rb
# :call-seq:
#   ARGF.readlines(sep=$/)     -> array
#   ARGF.readlines(limit)      -> array
#   ARGF.readlines(sep, limit) -> array
#
#   ARGF.to_a(sep=$/)     -> array
#   ARGF.to_a(limit)      -> array
#   ARGF.to_a(sep, limit) -> array
```

### Category

```rb
# :category: Utilities
```

### Sections

```rb
# :section: Expiry methods
# methods relating to expiring

def expire!
def expired?
...
```

### Using tomdoc

```rb
# :markup: TomDoc
```

Place this at the beginning of the file.

## Also see
{: .-one-column}

* <http://rdoc.rubyforge.org/RDoc/Markup.html>
<<<<<<< HEAD
* <http://www.rubydoc.info/gems/yard/file/docs/GettingStarted.md>
=======
* <https://www.rubydoc.info/gems/yard/file/docs/GettingStarted.md>
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
{: .-also-see}
