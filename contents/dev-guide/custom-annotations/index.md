---
title: 'Custom annotations'
template: main.jade
---

# Using custom annotations

If you're finding yourself having to translate the same attribute all the time, you can tell gettext to pick it up as a translation. After that its up to you to define how its being translated. For example, if you're translating the `placeholder` attribute on every input, chances are you're doing something like:

```xml
<input placeholder="{{ 'Input something here' | translate }}">
```

If you want to skip having to add the data-binding and `translate` filter to every input, you can specify `placeholder` as an attribute in your Grunt config:

```javascript
grunt.initConfig({
  nggettext_extract: {
    pot: {
      options: {
        attributes: ['placeholder']
      },
      files: {
        'po/template.pot': ['src/views/*.html']
      }
    },
  },
})
```

Afterwards you need to implement a directive that translates the `placeholder` attribute. Here's a simple example:

```javascript
angular.module('myModule').directive('placeholder', ['gettextCatalog', function(gettextCatalog){
  return {
    restrict: 'A',
    link: function(scope, element, attrs){
      var translatedPlaceholder = gettextCatalog.getString(attrs.placeholder);
      element.attr('placeholder', translatedPlaceholder);
    }
  };
}]);
```

And finally you can continue using the placeholder attribute as usual:

```xml
<input placeholder="Input something here">
```

## Plurals

If you've specified custom attributes, gettext also looks for plurals by adding `-plural` and `-n` to the attribute name, e.g. `placeholder-plural` and `placeholder-n`:

```xml
<input placeholder="Input a hobbit name" placeholder-n="hobbitCount" placeholder-plural="Input names for {{$count}} hobbits"/>
```

Keep in mind that its up to you how the translation is being handled. In this case you'll need to use `gettextCatalog.getPlural`.

## Comments

Comments are handled in the same way: gettext looks for `[custom attribute]-comment`, e.g. `placeholder-comment`:

```xml
<input placeholder-comment="Name for a creature that is short in stature" placeholder="Hobbit name">
```
The comments will appear in the extracted text catalog and will help the translator.
