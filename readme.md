# Marionette.Async: Deprecated

Add async / deferred rendering and template loading to [Backbone.Marionette](https://github.com/derickbailey/backbone.marionette)

## Deprecation Notice

**Marionette.Async is no longer supported by the Marionette developers
or maintainers.**

After much discussion we decided the cost and complexities that 
Marionette.Async introduced were too high. The patterns that were used
in Async to facilitate the asynchronous loading of data, loading of
templates, rendering and other features were at a cost of performance
in the application, simplicity in the code, and maintainability in
moving forward.

Marionette.Async will no longer be updated. No issues will be fixed
and no release will be made with Marionette v1.0. 

If you wish to continue using Marionette.Async, you can fork this 
project and maintain it as you see fit.

Thanks,

  -- The Marionette Core Team

## Download

To add support for asynchronously loading templates and rendering views, 
you need to download and include one of the following builds in your project:

### Standard Builds

Development: [backbone.marionette.async.js](https://raw.github.com/derickbailey/backbone.marionette.async/master/lib/backbone.marionette.async.js)

Production: [backbone.marionette.async.min.js](https://raw.github.com/derickbailey/backbone.marionette.async/master/lib/backbone.marionette.async.min.js)

### AMD/RequireJS Builds

Development: [backbone.marionette.async.js](https://raw.github.com/derickbailey/backbone.marionette.async/master/lib/amd/backbone.marionette.async.js)

Production: [backbone.marionette.async.min.js](https://raw.github.com/derickbailey/backbone.marionette.async/master/lib/amd/backbone.marionette.async.min.js)

## Documentation

### Basic Usage

To use Marionette.Async, you must include one of the above downloads in
your project, after Marionette has been included:

```html
<script src="/js/json2.js"></script>
<script src="/js/jquery.js"></script>
<script src="/js/underscore.js"></script>
<script src="/js/backbone.js"></script>
<script src="/js/backbone.marionette.js"></script>

<script src="/js/backbone.marionette.async.js"></script>
```

Once that has been included, several core functions on several of the
Marionette objects will be overwritten with asynchronous counterparts.

**Note that this is destructive change.** You cannot switch
back and forth between sync/async objects. Once included in the page, all
of the specified methods will run with async capabilities.

### Marionette.Async.Region

The region manager `show` method takes advantage of jQuery's
deferred cababilities, allowing for some very advanced techniques
to be used for rendering views.

To use a deferred, a view that is shown via a region manager
must return a jQuery deferred object from the `render` method:

```js
DeferredView = Backbone.View.extend({
  render: function(){
    var that = this;
    var data = this.serializeData();
    var dfd = jQuery.Deferred();

    this.getTemplate(function(template){
      var html = that.renderTemplate(template, data);

      that.$el.html(html);

      if (that.onRender){
        that.onRender();
      }

      dfd.resolve();
    });

    return dfd.promise();
  }
});

var view = new DeferredView();
MyApp.mainRegion.show(view);
```

The region manager will wait until the deferred object is resolved
before it attached the view's `el` to the DOM and displays it.

### Marionette.Async.ItemView

The `render` method returns a jQuery deferred object, allowing
you to know when the rendering completes. 

### Marionette.Async.CollectionView

The `render` method returns a jQuery deferred object, allowing
you to know when the rendering completes. This deferred object
is resolved after all of the child views have been rendered.

### Marionette.Async.CompositeView

A composite view returns a jQuery deferred object from the
`render` method. This allows you to know when the rendering for
the entire composite structure has been completed.

```js
MyComp = Backbone.Marionette.CompositeView.extend({...});

myComp = new MyComp().render().done(function(){
  // the entire composite is now rendered. do stuff here
});
```

### Marionette.Async.TemplateCache

The default template retrieval is to select the template contents
from the DOM using jQuery. If you wish to change the way this
works, you can override the `loadTemplate` method on the
`TemplateCache` object.

```js
Backbone.Marionette.TemplateCache.prototype.loadTemplate = function(templateId, callback){
  // load your template here, returning it or a deferred
  // object that resolves with the template as the only param
  var myTemplate = ...;

  // send the template back
  callback(myTemplate);
}
```

For example, if you want to load templates asychronously from the
server, instead of from the DOM, you could replace 
`loadTemplate` function.

If a "template.html" file exists on the server, with this in it:

```html
<script id="my-template" type="text/template">
  <div>some template stuff</div>
</script>
```

Then the `loadTemplate` implementation may look like this:

```js
Backbone.Marionette.TemplateCache.loadTemplate = function(templateId, callback){
  var that = this;
  var url = templateId + ".html";

  $.get(url, function(templateHtml){
    var template = $(templateHtml).find(templateId);
    callback(template);
  });
}
```

This will use jQuery to asynchronously retrieve the template from
the server. When the `get` completes, the callback function will
select the template from the resulting HTML and then call the
`callback` function to send it in to the template cache and allow
it to be used for rendering.

## Compatibility And Requirements

Marionette.Async currently works with [Backbone.Marionette](https://github.com/derickbailey/backbone.marionette) v0.9.11

## Release Notes

For change logs and release notes, see the
[changelog](https://github.com/derickbailey/backbone.marionette.async/blob/master/changelog.md) file.

## Legal Mumbo Jumbo (MIT License)

Copyright (c) 2012 Derick Bailey, Muted Solutions, LLC

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
