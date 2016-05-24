![Backbone.js](docs/images/backbone.png)

Backbone.js gives structure to web applications by providing **models** with key-value binding and custom events, **collections** with a rich API of enumerable functions, **views** with declarative event handling, and connects it all to your existing API over a RESTful JSON interface.

The project is [hosted on GitHub](http://github.com/jashkenas/backbone/), and the [annotated source code](docs/backbone.html) is available, as well as an online [test suite](test/), an [example application](examples/todos/index.html), a [list of tutorials](https://github.com/jashkenas/backbone/wiki/Tutorials%2C-blog-posts-and-example-sites) and a [long list of real-world projects](#examples) that use Backbone. Backbone is available for use under the [MIT software license](http://github.com/jashkenas/backbone/blob/master/LICENSE).

You can report bugs and discuss features on the [GitHub issues page](http://github.com/jashkenas/backbone/issues), on Freenode IRC in the <tt>#documentcloud</tt> channel, post questions to the [Google Group](https://groups.google.com/forum/#!forum/backbonejs), add pages to the [wiki](https://github.com/jashkenas/backbone/wiki) or send tweets to [@documentcloud](http://twitter.com/documentcloud).

_Backbone is an open-source component of [DocumentCloud](http://documentcloud.org/)._

## Downloads & Dependencies <span style="padding-left: 7px; font-size:11px; font-weight: normal;" class="interface">(Right-click, and use "Save As")</span>

<table>

<tbody>

<tr>

<td>[Development Version (1.3.3)](backbone.js)</td>

<td class="text">_72kb, Full source, tons of comments_</td>

</tr>

<tr>

<td>[Production Version (1.3.3)](backbone-min.js)</td>

<td class="text" style="line-height: 16px;">_7.6kb, Packed and gzipped_  
<small>([Source Map](backbone-min.map))</small></td>

</tr>

<tr>

<td>[Edge Version (master)](https://raw.github.com/jashkenas/backbone/master/backbone.js)</td>

<td>_Unreleased, use at your own risk_ [![](https://travis-ci.org/jashkenas/backbone.png)](https://travis-ci.org/jashkenas/backbone) </td>

</tr>

</tbody>

</table>

Backbone's only hard dependency is [Underscore.js](http://underscorejs.org/) <small>( >= 1.8.3)</small>. For RESTful persistence and DOM manipulation with [Backbone.View](#View), include **[jQuery](https://jquery.com/)** ( >= 1.11.0), and **[json2.js](https://github.com/douglascrockford/JSON-js)** for older Internet Explorer support. _(Mimics of the Underscore and jQuery APIs, such as [Lodash](https://lodash.com/) and [Zepto](http://zeptojs.com/), will also tend to work, with varying degrees of compatibility.)_

## Getting Started

When working on a web application that involves a lot of JavaScript, one of the first things you learn is to stop tying your data to the DOM. It's all too easy to create JavaScript applications that end up as tangled piles of jQuery selectors and callbacks, all trying frantically to keep data in sync between the HTML UI, your JavaScript logic, and the database on your server. For rich client-side applications, a more structured approach is often helpful.

With Backbone, you represent your data as [Models](#Model), which can be created, validated, destroyed, and saved to the server. Whenever a UI action causes an attribute of a model to change, the model triggers a _"change"_ event; all the [Views](#View) that display the model's state can be notified of the change, so that they are able to respond accordingly, re-rendering themselves with the new information. In a finished Backbone app, you don't have to write the glue code that looks into the DOM to find an element with a specific _id_, and update the HTML manually — when the model changes, the views simply update themselves.

Philosophically, Backbone is an attempt to discover the minimal set of data-structuring (models and collections) and user interface (views and URLs) primitives that are generally useful when building web applications with JavaScript. In an ecosystem where overarching, decides-everything-for-you frameworks are commonplace, and many libraries require your site to be reorganized to suit their look, feel, and default behavior — Backbone should continue to be a tool that gives you the _freedom_ to design the full experience of your web application.

If you're new here, and aren't yet quite sure what Backbone is for, start by browsing the [list of Backbone-based projects](#examples).

Many of the code examples in this documentation are runnable, because Backbone is included on this page. Click the _play_ button to execute them.

## Models and Views

![Model-View Separation.](docs/images/intro-model-view.svg)

The single most important thing that Backbone can help you with is keeping your business logic separate from your user interface. When the two are entangled, change is hard; when logic doesn't depend on UI, your interface becomes easier to work with.

<div class="columns">

<div class="col-50">**Model**

*   Orchestrates data and business logic.
*   Loads and saves from the server.
*   Emits events when data changes.

</div>

<div class="col-50">**View**

*   Listens for changes and renders UI.
*   Handles user input and interactivity.
*   Sends captured input to the model.

</div>

</div>

A **Model** manages an internal table of data attributes, and triggers <tt>"change"</tt> events when any of its data is modified. Models handle syncing data with a persistence layer — usually a REST API with a backing database. Design your models as the atomic reusable objects containing all of the helpful functions for manipulating their particular bit of data. Models should be able to be passed around throughout your app, and used anywhere that bit of data is needed.

A **View** is an atomic chunk of user interface. It often renders the data from a specific model, or number of models — but views can also be data-less chunks of UI that stand alone. Models should be generally unaware of views. Instead, views listen to the model <tt>"change"</tt> events, and react or re-render themselves appropriately.

## Collections

![Model Collections.](docs/images/intro-collections.svg)

A **Collection** helps you deal with a group of related models, handling the loading and saving of new models to the server and providing helper functions for performing aggregations or computations against a list of models. Aside from their own events, collections also proxy through all of the events that occur to models within them, allowing you to listen in one place for any change that might happen to any model in the collection.

## API Integration

Backbone is pre-configured to sync with a RESTful API. Simply create a new Collection with the <tt>url</tt> of your resource endpoint:

<pre>var Books = Backbone.Collection.extend({
  url: '/books'
});
</pre>

The **Collection** and **Model** components together form a direct mapping of REST resources using the following methods:

<pre>GET  /books/ .... collection.fetch();
POST /books/ .... collection.create();
GET  /books/1 ... model.fetch();
PUT  /books/1 ... model.save();
DEL  /books/1 ... model.destroy();
</pre>

When fetching raw JSON data from an API, a **Collection** will automatically populate itself with data formatted as an array, while a **Model** will automatically populate itself with data formatted as an object:

<pre>[{"id": 1}] ..... populates a Collection with one model.
{"id": 1} ....... populates a Model with one attribute.
</pre>

However, it's fairly common to encounter APIs that return data in a different format than what Backbone expects. For example, consider fetching a **Collection** from an API that returns the real data array wrapped in metadata:

<pre>{
  "page": 1,
  "limit": 10,
  "total": 2,
  "books": [
    {"id": 1, "title": "Pride and Prejudice"},
    {"id": 4, "title": "The Great Gatsby"}
  ]
}
</pre>

In the above example data, a **Collection** should populate using the <tt>"books"</tt> array rather than the root object structure. This difference is easily reconciled using a <tt>parse</tt> method that returns (or transforms) the desired portion of API data:

<pre>var Books = Backbone.Collection.extend({
  url: '/books',
  parse: function(data) {
    return data.books;
  }
});
</pre>

## View Rendering

![View rendering.](docs/images/intro-views.svg)

Each **View** manages the rendering and user interaction within its own DOM element. If you're strict about not allowing views to reach outside of themselves, it helps keep your interface flexible — allowing views to be rendered in isolation in any place where they might be needed.

Backbone remains unopinionated about the process used to render **View** objects and their subviews into UI: you define how your models get translated into HTML (or SVG, or Canvas, or something even more exotic). It could be as prosaic as a simple [Underscore template](http://underscorejs.org/#template), or as fancy as the [React virtual DOM](http://facebook.github.io/react/docs/tutorial.html). Some basic approaches to rendering views can be found in the [Backbone primer](https://github.com/jashkenas/backbone/wiki/Backbone%2C-The-Primer).

## Routing with URLs

![Routing](docs/images/intro-routing.svg)

In rich web applications, we still want to provide linkable, bookmarkable, and shareable URLs to meaningful locations within an app. Use the **Router** to update the browser URL whenever the user reaches a new "place" in your app that they might want to bookmark or share. Conversely, the **Router** detects changes to the URL — say, pressing the "Back" button — and can tell your application exactly where you are now.

## Backbone.Events

**Events** is a module that can be mixed in to any object, giving the object the ability to bind and trigger custom named events. Events do not have to be declared before they are bound, and may take passed arguments. For example:

<pre class="runnable">var object = {};

_.extend(object, Backbone.Events);

object.on("alert", function(msg) {
  alert("Triggered " + msg);
});

object.trigger("alert", "an event");
</pre>

For example, to make a handy event dispatcher that can coordinate events among different areas of your application: <tt>var dispatcher = _.clone(Backbone.Events)</tt>

**on**`object.on(event, callback, [context])`<span class="alias">Alias: bind</span>  
Bind a **callback** function to an object. The callback will be invoked whenever the **event** is fired. If you have a large number of different events on a page, the convention is to use colons to namespace them: <tt>"poll:start"</tt>, or <tt>"change:selection"</tt>. The event string may also be a space-delimited list of several events...

<pre>book.on("change:title change:author", ...);
</pre>

Callbacks bound to the special <tt>"all"</tt> event will be triggered when any event occurs, and are passed the name of the event as the first argument. For example, to proxy all events from one object to another:

<pre>proxy.on("all", function(eventName) {
  object.trigger(eventName);
});
</pre>

All Backbone event methods also support an event map syntax, as an alternative to positional arguments:

<pre>book.on({
  "change:author": authorPane.update,
  "change:title change:subtitle": titleView.update,
  "destroy": bookView.remove
});
</pre>

To supply a **context** value for <tt>this</tt> when the callback is invoked, pass the optional last argument: <tt>model.on('change', this.render, this)</tt> or <tt>model.on({change: this.render}, this)</tt>.

**off**`object.off([event], [callback], [context])`<span class="alias">Alias: unbind</span>  
Remove a previously-bound **callback** function from an object. If no **context** is specified, all of the versions of the callback with different contexts will be removed. If no callback is specified, all callbacks for the **event** will be removed. If no event is specified, callbacks for _all_ events will be removed.

<pre>// Removes just the `onChange` callback.
object.off("change", onChange);

// Removes all "change" callbacks.
object.off("change");

// Removes the `onChange` callback for all events.
object.off(null, onChange);

// Removes all callbacks for `context` for all events.
object.off(null, null, context);

// Removes all callbacks on `object`.
object.off();
</pre>

Note that calling <tt>model.off()</tt>, for example, will indeed remove _all_ events on the model — including events that Backbone uses for internal bookkeeping.

**trigger**`object.trigger(event, [*args])`  
Trigger callbacks for the given **event**, or space-delimited list of events. Subsequent arguments to **trigger** will be passed along to the event callbacks.

**once**`object.once(event, callback, [context])`  
Just like [on](#Events-on), but causes the bound callback to fire only once before being removed. Handy for saying "the next time that X happens, do this". When multiple events are passed in using the space separated syntax, the event will fire once for every event you passed in, not once for a combination of all events

**listenTo**`object.listenTo(other, event, callback)`  
Tell an **object** to listen to a particular event on an **other** object. The advantage of using this form, instead of <tt>other.on(event, callback, object)</tt>, is that **listenTo** allows the **object** to keep track of the events, and they can be removed all at once later on. The **callback** will always be called with **object** as context.

<pre>view.listenTo(model, 'change', view.render);
</pre>

**stopListening**`object.stopListening([other], [event], [callback])`  
Tell an **object** to stop listening to events. Either call **stopListening** with no arguments to have the **object** remove all of its [registered](#Events-listenTo) callbacks ... or be more precise by telling it to remove just the events it's listening to on a specific object, or a specific event, or just a specific callback.

<pre>view.stopListening();

view.stopListening(model);
</pre>

**listenToOnce**`object.listenToOnce(other, event, callback)`  
Just like [listenTo](#Events-listenTo), but causes the bound callback to fire only once before being removed.

**Catalog of Events**  
Here's the complete list of built-in Backbone events, with arguments. You're also free to trigger your own events on Models, Collections and Views as you see fit. The <tt>Backbone</tt> object itself mixes in <tt>Events</tt>, and can be used to emit any global events that your application needs.

*   **"add"** (model, collection, options) — when a model is added to a collection.
*   **"remove"** (model, collection, options) — when a model is removed from a collection.
*   **"update"** (collection, options) — single event triggered after any number of models have been added or removed from a collection.
*   **"reset"** (collection, options) — when the collection's entire contents have been [reset](#Collection-reset).
*   **"sort"** (collection, options) — when the collection has been re-sorted.
*   **"change"** (model, options) — when a model's attributes have changed.
*   **"change:[attribute]"** (model, value, options) — when a specific attribute has been updated.
*   **"destroy"** (model, collection, options) — when a model is [destroyed](#Model-destroy).
*   **"request"** (model_or_collection, xhr, options) — when a model or collection has started a request to the server.
*   **"sync"** (model_or_collection, response, options) — when a model or collection has been successfully synced with the server.
*   **"error"** (model_or_collection, response, options) — when a model's or collection's request to the server has failed.
*   **"invalid"** (model, error, options) — when a model's [validation](#Model-validate) fails on the client.
*   **"route:[name]"** (params) — Fired by the router when a specific route is matched.
*   **"route"** (route, params) — Fired by the router when _any_ route has been matched.
*   **"route"** (router, route, params) — Fired by history when _any_ route has been matched.
*   **"all"** — this special event fires for _any_ triggered event, passing the event name as the first argument followed by all trigger arguments.

Generally speaking, when calling a function that emits an event (<tt>model.set</tt>, <tt>collection.add</tt>, and so on...), if you'd like to prevent the event from being triggered, you may pass <tt>{silent: true}</tt> as an option. Note that this is _rarely_, perhaps even never, a good idea. Passing through a specific flag in the options for your event callback to look at, and choose to ignore, will usually work out better.

## Backbone.Model

**Models** are the heart of any JavaScript application, containing the interactive data as well as a large part of the logic surrounding it: conversions, validations, computed properties, and access control. You extend **Backbone.Model** with your domain-specific methods, and **Model** provides a basic set of functionality for managing changes.

The following is a contrived example, but it demonstrates defining a model with a custom method, setting an attribute, and firing an event keyed to changes in that specific attribute. After running this code once, <tt>sidebar</tt> will be available in your browser's console, so you can play around with it.

<pre class="runnable">var Sidebar = Backbone.Model.extend({
  promptColor: function() {
    var cssColor = prompt("Please enter a CSS color:");
    this.set({color: cssColor});
  }
});

window.sidebar = new Sidebar;

sidebar.on('change:color', function(model, color) {
  $('#sidebar').css({background: color});
});

sidebar.set({color: 'white'});

sidebar.promptColor();
</pre>

**extend**`Backbone.Model.extend(properties, [classProperties])`  
To create a **Model** class of your own, you extend **Backbone.Model** and provide instance **properties**, as well as optional **classProperties** to be attached directly to the constructor function.

**extend** correctly sets up the prototype chain, so subclasses created with **extend** can be further extended and subclassed as far as you like.

<pre>var Note = Backbone.Model.extend({

  initialize: function() { ... },

  author: function() { ... },

  coordinates: function() { ... },

  allowedToEdit: function(account) {
    return true;
  }

});

var PrivateNote = Note.extend({

  allowedToEdit: function(account) {
    return account.owns(this);
  }

});
</pre>

Brief aside on <tt>super</tt>: JavaScript does not provide a simple way to call super — the function of the same name defined higher on the prototype chain. If you override a core function like <tt>set</tt>, or <tt>save</tt>, and you want to invoke the parent object's implementation, you'll have to explicitly call it, along these lines:

<pre>var Note = Backbone.Model.extend({
  set: function(attributes, options) {
    Backbone.Model.prototype.set.apply(this, arguments);
    ...
  }
});
</pre>

**constructor / initialize**`new Model([attributes], [options])`  
When creating an instance of a model, you can pass in the initial values of the **attributes**, which will be [set](#Model-set) on the model. If you define an **initialize** function, it will be invoked when the model is created.

<pre>new Book({
  title: "One Thousand and One Nights",
  author: "Scheherazade"
});
</pre>

In rare cases, if you're looking to get fancy, you may want to override **constructor**, which allows you to replace the actual constructor function for your model.

<pre>var Library = Backbone.Model.extend({
  constructor: function() {
    this.books = new Books();
    Backbone.Model.apply(this, arguments);
  },
  parse: function(data, options) {
    this.books.reset(data.books);
    return data.library;
  }
});
</pre>

If you pass a <tt>{collection: ...}</tt> as the **options**, the model gains a <tt>collection</tt> property that will be used to indicate which collection the model belongs to, and is used to help compute the model's [url](#Model-url). The <tt>model.collection</tt> property is normally created automatically when you first add a model to a collection. Note that the reverse is not true, as passing this option to the constructor will not automatically add the model to the collection. Useful, sometimes.

If <tt>{parse: true}</tt> is passed as an **option**, the **attributes** will first be converted by [parse](#Model-parse) before being [set](#Model-set) on the model.

**get**`model.get(attribute)`  
Get the current value of an attribute from the model. For example: <tt>note.get("title")</tt>

**set**`model.set(attributes, [options])`  
Set a hash of attributes (one or many) on the model. If any of the attributes change the model's state, a <tt>"change"</tt> event will be triggered on the model. Change events for specific attributes are also triggered, and you can bind to those as well, for example: <tt>change:title</tt>, and <tt>change:content</tt>. You may also pass individual keys and values.

<pre>note.set({title: "March 20", content: "In his eyes she eclipses..."});

book.set("title", "A Scandal in Bohemia");
</pre>

**escape**`model.escape(attribute)`  
Similar to [get](#Model-get), but returns the HTML-escaped version of a model's attribute. If you're interpolating data from the model into HTML, using **escape** to retrieve attributes will prevent [XSS](http://en.wikipedia.org/wiki/Cross-site_scripting) attacks.

<pre class="runnable">var hacker = new Backbone.Model({
  name: "<script>alert('xss')</script>"
});

alert(hacker.escape('name'));
</pre>

**has**`model.has(attribute)`  
Returns <tt>true</tt> if the attribute is set to a non-null or non-undefined value.

<pre>if (note.has("title")) {
  ...
}
</pre>

**unset**`model.unset(attribute, [options])`  
Remove an attribute by deleting it from the internal attributes hash. Fires a <tt>"change"</tt> event unless <tt>silent</tt> is passed as an option.

**clear**`model.clear([options])`  
Removes all attributes from the model, including the <tt>id</tt> attribute. Fires a <tt>"change"</tt> event unless <tt>silent</tt> is passed as an option.

**id**`model.id`  
A special property of models, the **id** is an arbitrary string (integer id or UUID). If you set the **id** in the attributes hash, it will be copied onto the model as a direct property. `model.id` should not be manipulated directly, it should be modified only via `model.set('id', …)`. Models can be retrieved by id from collections, and the id is used to generate model URLs by default.

**idAttribute**`model.idAttribute`  
A model's unique identifier is stored under the <tt>id</tt> attribute. If you're directly communicating with a backend (CouchDB, MongoDB) that uses a different unique key, you may set a Model's <tt>idAttribute</tt> to transparently map from that key to <tt>id</tt>.

<pre class="runnable">var Meal = Backbone.Model.extend({
  idAttribute: "_id"
});

var cake = new Meal({ _id: 1, name: "Cake" });
alert("Cake id: " + cake.id);
</pre>

**cid**`model.cid`  
A special property of models, the **cid** or client id is a unique identifier automatically assigned to all models when they're first created. Client ids are handy when the model has not yet been saved to the server, and does not yet have its eventual true **id**, but already needs to be visible in the UI.

**attributes**`model.attributes`  
The **attributes** property is the internal hash containing the model's state — usually (but not necessarily) a form of the JSON object representing the model data on the server. It's often a straightforward serialization of a row from the database, but it could also be client-side computed state.

Please use [set](#Model-set) to update the **attributes** instead of modifying them directly. If you'd like to retrieve and munge a copy of the model's attributes, use <tt>_.clone(model.attributes)</tt> instead.

Due to the fact that [Events](#Events) accepts space separated lists of events, attribute names should not include spaces.

**changed**`model.changed`  
The **changed** property is the internal hash containing all the attributes that have changed since its last [set](#Model-set). Please do not update **changed** directly since its state is internally maintained by [set](#Model-set). A copy of **changed** can be acquired from [changedAttributes](#Model-changedAttributes).

**defaults**`model.defaults or model.defaults()`  
The **defaults** hash (or function) can be used to specify the default attributes for your model. When creating an instance of the model, any unspecified attributes will be set to their default value.

<pre class="runnable">var Meal = Backbone.Model.extend({
  defaults: {
    "appetizer":  "caesar salad",
    "entree":     "ravioli",
    "dessert":    "cheesecake"
  }
});

alert("Dessert will be " + (new Meal).get('dessert'));
</pre>

Remember that in JavaScript, objects are passed by reference, so if you include an object as a default value, it will be shared among all instances. Instead, define **defaults** as a function.

**toJSON**`model.toJSON([options])`  
Return a shallow copy of the model's [attributes](#Model-attributes) for JSON stringification. This can be used for persistence, serialization, or for augmentation before being sent to the server. The name of this method is a bit confusing, as it doesn't actually return a JSON string — but I'm afraid that it's the way that the [JavaScript API for **JSON.stringify**](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#toJSON_behavior) works.

<pre class="runnable">var artist = new Backbone.Model({
  firstName: "Wassily",
  lastName: "Kandinsky"
});

artist.set({birthday: "December 16, 1866"});

alert(JSON.stringify(artist));
</pre>

**sync**`model.sync(method, model, [options])`  
Uses [Backbone.sync](#Sync) to persist the state of a model to the server. Can be overridden for custom behavior.

**fetch**`model.fetch([options])`  
Merges the model's state with attributes fetched from the server by delegating to [Backbone.sync](#Sync). Returns a [jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR). Useful if the model has never been populated with data, or if you'd like to ensure that you have the latest server state. Triggers a <tt>"change"</tt> event if the server's state differs from the current attributes. <tt>fetch</tt> accepts <tt>success</tt> and <tt>error</tt> callbacks in the options hash, which are both passed <tt>(model, response, options)</tt> as arguments.

<pre>// Poll every 10 seconds to keep the channel model up-to-date.
setInterval(function() {
  channel.fetch();
}, 10000);
</pre>

**save**`model.save([attributes], [options])`  
Save a model to your database (or alternative persistence layer), by delegating to [Backbone.sync](#Sync). Returns a [jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR) if validation is successful and <tt>false</tt> otherwise. The **attributes** hash (as in [set](#Model-set)) should contain the attributes you'd like to change — keys that aren't mentioned won't be altered — but, a _complete representation_ of the resource will be sent to the server. As with <tt>set</tt>, you may pass individual keys and values instead of a hash. If the model has a [validate](#Model-validate) method, and validation fails, the model will not be saved. If the model [isNew](#Model-isNew), the save will be a <tt>"create"</tt> (HTTP <tt>POST</tt>), if the model already exists on the server, the save will be an <tt>"update"</tt> (HTTP <tt>PUT</tt>).

If instead, you'd only like the _changed_ attributes to be sent to the server, call <tt>model.save(attrs, {patch: true})</tt>. You'll get an HTTP <tt>PATCH</tt> request to the server with just the passed-in attributes.

Calling <tt>save</tt> with new attributes will cause a <tt>"change"</tt> event immediately, a <tt>"request"</tt> event as the Ajax request begins to go to the server, and a <tt>"sync"</tt> event after the server has acknowledged the successful change. Pass <tt>{wait: true}</tt> if you'd like to wait for the server before setting the new attributes on the model.

In the following example, notice how our overridden version of <tt>Backbone.sync</tt> receives a <tt>"create"</tt> request the first time the model is saved and an <tt>"update"</tt> request the second time.

<pre class="runnable">Backbone.sync = function(method, model) {
  alert(method + ": " + JSON.stringify(model));
  model.set('id', 1);
};

var book = new Backbone.Model({
  title: "The Rough Riders",
  author: "Theodore Roosevelt"
});

book.save();

book.save({author: "Teddy"});
</pre>

**save** accepts <tt>success</tt> and <tt>error</tt> callbacks in the options hash, which will be passed the arguments <tt>(model, response, options)</tt>. If a server-side validation fails, return a non-<tt>200</tt> HTTP response code, along with an error response in text or JSON.

<pre>book.save("author", "F.D.R.", {error: function(){ ... }});
</pre>

**destroy**`model.destroy([options])`  
Destroys the model on the server by delegating an HTTP <tt>DELETE</tt> request to [Backbone.sync](#Sync). Returns a [jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR) object, or <tt>false</tt> if the model [isNew](#Model-isNew). Accepts <tt>success</tt> and <tt>error</tt> callbacks in the options hash, which will be passed <tt>(model, response, options)</tt>. Triggers a <tt>"destroy"</tt> event on the model, which will bubble up through any collections that contain it, a <tt>"request"</tt> event as it begins the Ajax request to the server, and a <tt>"sync"</tt> event, after the server has successfully acknowledged the model's deletion. Pass <tt>{wait: true}</tt> if you'd like to wait for the server to respond before removing the model from the collection.

<pre>book.destroy({success: function(model, response) {
  ...
}});
</pre>

**Underscore Methods (9)**  
Backbone proxies to **Underscore.js** to provide 9 object functions on **Backbone.Model**. They aren't all documented here, but you can take a look at the Underscore documentation for the full details…

*   [keys](http://underscorejs.org/#keys)
*   [values](http://underscorejs.org/#values)
*   [pairs](http://underscorejs.org/#pairs)
*   [invert](http://underscorejs.org/#invert)
*   [pick](http://underscorejs.org/#pick)
*   [omit](http://underscorejs.org/#omit)
*   [chain](http://underscorejs.org/#chain)
*   [isEmpty](http://underscorejs.org/#isEmpty)

<pre>user.pick('first_name', 'last_name', 'email');

chapters.keys().join(', ');
</pre>

**validate**`model.validate(attributes, options)`  
This method is left undefined and you're encouraged to override it with any custom validation logic you have that can be performed in JavaScript. By default <tt>save</tt> checks **validate** before setting any attributes but you may also tell <tt>set</tt> to validate the new attributes by passing <tt>{validate: true}</tt> as an option.  
The **validate** method receives the model attributes as well as any options passed to <tt>set</tt> or <tt>save</tt>. If the attributes are valid, don't return anything from **validate**; if they are invalid return an error of your choosing. It can be as simple as a string error message to be displayed, or a complete error object that describes the error programmatically. If **validate** returns an error, <tt>save</tt> will not continue, and the model attributes will not be modified on the server. Failed validations trigger an <tt>"invalid"</tt> event, and set the <tt>validationError</tt> property on the model with the value returned by this method.

<pre class="runnable">var Chapter = Backbone.Model.extend({
  validate: function(attrs, options) {
    if (attrs.end < attrs.start) {
      return "can't end before it starts";
    }
  }
});

var one = new Chapter({
  title : "Chapter One: The Beginning"
});

one.on("invalid", function(model, error) {
  alert(model.get("title") + " " + error);
});

one.save({
  start: 15,
  end:   10
});
</pre>

<tt>"invalid"</tt> events are useful for providing coarse-grained error messages at the model or collection level.

**validationError**`model.validationError`  
The value returned by [validate](#Model-validate) during the last failed validation.

**isValid**`model.isValid()`  
Run [validate](#Model-validate) to check the model state.

<pre class="runnable">var Chapter = Backbone.Model.extend({
  validate: function(attrs, options) {
    if (attrs.end < attrs.start) {
      return "can't end before it starts";
    }
  }
});

var one = new Chapter({
  title : "Chapter One: The Beginning"
});

one.set({
  start: 15,
  end:   10
});

if (!one.isValid()) {
  alert(one.get("title") + " " + one.validationError);
}
</pre>

**url**`model.url()`  
Returns the relative URL where the model's resource would be located on the server. If your models are located somewhere else, override this method with the correct logic. Generates URLs of the form: <tt>"[collection.url]/[id]"</tt> by default, but you may override by specifying an explicit <tt>urlRoot</tt> if the model's collection shouldn't be taken into account.

Delegates to [Collection#url](#Collection-url) to generate the URL, so make sure that you have it defined, or a [urlRoot](#Model-urlRoot) property, if all models of this class share a common root URL. A model with an id of <tt>101</tt>, stored in a [Backbone.Collection](#Collection) with a <tt>url</tt> of <tt>"/documents/7/notes"</tt>, would have this URL: <tt>"/documents/7/notes/101"</tt>

**urlRoot**`model.urlRoot or model.urlRoot()`  
Specify a <tt>urlRoot</tt> if you're using a model _outside_ of a collection, to enable the default [url](#Model-url) function to generate URLs based on the model id. <tt>"[urlRoot]/id"</tt>  
Normally, you won't need to define this. Note that <tt>urlRoot</tt> may also be a function.

<pre class="runnable">var Book = Backbone.Model.extend({urlRoot : '/books'});

var solaris = new Book({id: "1083-lem-solaris"});

alert(solaris.url());
</pre>

**parse**`model.parse(response, options)`  
**parse** is called whenever a model's data is returned by the server, in [fetch](#Model-fetch), and [save](#Model-save). The function is passed the raw <tt>response</tt> object, and should return the attributes hash to be [set](#Model-set) on the model. The default implementation is a no-op, simply passing through the JSON response. Override this if you need to work with a preexisting API, or better namespace your responses.

If you're working with a Rails backend that has a version prior to 3.1, you'll notice that its default <tt>to_json</tt> implementation includes a model's attributes under a namespace. To disable this behavior for seamless Backbone integration, set:

<pre>ActiveRecord::Base.include_root_in_json = false
</pre>

**clone**`model.clone()`  
Returns a new instance of the model with identical attributes.

**isNew**`model.isNew()`  
Has this model been saved to the server yet? If the model does not yet have an <tt>id</tt>, it is considered to be new.

**hasChanged**`model.hasChanged([attribute])`  
Has the model changed since its last [set](#Model-set)? If an **attribute** is passed, returns <tt>true</tt> if that specific attribute has changed.

Note that this method, and the following change-related ones, are only useful during the course of a <tt>"change"</tt> event.

<pre>book.on("change", function() {
  if (book.hasChanged("title")) {
    ...
  }
});
</pre>

**changedAttributes**`model.changedAttributes([attributes])`  
Retrieve a hash of only the model's attributes that have changed since the last [set](#Model-set), or <tt>false</tt> if there are none. Optionally, an external **attributes** hash can be passed in, returning the attributes in that hash which differ from the model. This can be used to figure out which portions of a view should be updated, or what calls need to be made to sync the changes to the server.

**previous**`model.previous(attribute)`  
During a <tt>"change"</tt> event, this method can be used to get the previous value of a changed attribute.

<pre class="runnable">var bill = new Backbone.Model({
  name: "Bill Smith"
});

bill.on("change:name", function(model, name) {
  alert("Changed name from " + bill.previous("name") + " to " + name);
});

bill.set({name : "Bill Jones"});
</pre>

**previousAttributes**`model.previousAttributes()`  
Return a copy of the model's previous attributes. Useful for getting a diff between versions of a model, or getting back to a valid state after an error occurs.

## Backbone.Collection

Collections are ordered sets of models. You can bind <tt>"change"</tt> events to be notified when any model in the collection has been modified, listen for <tt>"add"</tt> and <tt>"remove"</tt> events, <tt>fetch</tt> the collection from the server, and use a full suite of [Underscore.js methods](#Collection-Underscore-Methods).

Any event that is triggered on a model in a collection will also be triggered on the collection directly, for convenience. This allows you to listen for changes to specific attributes in any model in a collection, for example: <tt>documents.on("change:selected", ...)</tt>

**extend**`Backbone.Collection.extend(properties, [classProperties])`  
To create a **Collection** class of your own, extend **Backbone.Collection**, providing instance **properties**, as well as optional **classProperties** to be attached directly to the collection's constructor function.

**model**`collection.model([attrs], [options])`  
Override this property to specify the model class that the collection contains. If defined, you can pass raw attributes objects (and arrays) to [add](#Collection-add), [create](#Collection-create), and [reset](#Collection-reset), and the attributes will be converted into a model of the proper type.

<pre>var Library = Backbone.Collection.extend({
  model: Book
});
</pre>

A collection can also contain polymorphic models by overriding this property with a constructor that returns a model.

<pre>var Library = Backbone.Collection.extend({

  model: function(attrs, options) {
    if (condition) {
      return new PublicDocument(attrs, options);
    } else {
      return new PrivateDocument(attrs, options);
    }
  }

});
</pre>

**modelId**`collection.modelId(attrs)`  
Override this method to return the value the collection will use to identify a model given its attributes. Useful for combining models from multiple tables with different [<tt>idAttribute</tt>](#Model-idAttribute) values into a single collection.

By default returns the value of the attributes' [<tt>idAttribute</tt>](#Model-idAttribute) from the collection's model class or failing that, <tt>id</tt>. If your collection uses a [model factory](#Collection-model) and those models have an <tt>idAttribute</tt> other than <tt>id</tt> you must override this method.

<pre class="runnable">var Library = Backbone.Collection.extend({
  modelId: function(attrs) {
    return attrs.type + attrs.id;
  }
});

var library = new Library([
  {type: 'dvd', id: 1},
  {type: 'vhs', id: 1}
]);

var dvdId = library.get('dvd1').id;
var vhsId = library.get('vhs1').id;
alert('dvd: ' + dvdId + ', vhs: ' + vhsId);
</pre>

**constructor / initialize**`new Backbone.Collection([models], [options])`  
When creating a Collection, you may choose to pass in the initial array of **models**. The collection's [comparator](#Collection-comparator) may be included as an option. Passing <tt>false</tt> as the comparator option will prevent sorting. If you define an **initialize** function, it will be invoked when the collection is created. There are a couple of options that, if provided, are attached to the collection directly: <tt>model</tt> and <tt>comparator</tt>.  
Pass <tt>null</tt> for <tt>models</tt> to create an empty Collection with <tt>options</tt>.

<pre>var tabs = new TabSet([tab1, tab2, tab3]);
var spaces = new Backbone.Collection(null, {
  model: Space
});
</pre>

**models**`collection.models`  
Raw access to the JavaScript array of models inside of the collection. Usually you'll want to use <tt>get</tt>, <tt>at</tt>, or the **Underscore methods** to access model objects, but occasionally a direct reference to the array is desired.

**toJSON**`collection.toJSON([options])`  
Return an array containing the attributes hash of each model (via [toJSON](#Model-toJSON)) in the collection. This can be used to serialize and persist the collection as a whole. The name of this method is a bit confusing, because it conforms to [JavaScript's JSON API](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#toJSON_behavior).

<pre class="runnable">var collection = new Backbone.Collection([
  {name: "Tim", age: 5},
  {name: "Ida", age: 26},
  {name: "Rob", age: 55}
]);

alert(JSON.stringify(collection));
</pre>

**sync**`collection.sync(method, collection, [options])`  
Uses [Backbone.sync](#Sync) to persist the state of a collection to the server. Can be overridden for custom behavior.

**Underscore Methods (46)**  
Backbone proxies to **Underscore.js** to provide 46 iteration functions on **Backbone.Collection**. They aren't all documented here, but you can take a look at the Underscore documentation for the full details…

Most methods can take an object or string to support model-attribute-style predicates or a function that receives the model instance as an argument.

*   [forEach (each)](http://underscorejs.org/#each)
*   [map (collect)](http://underscorejs.org/#map)
*   [reduce (foldl, inject)](http://underscorejs.org/#reduce)
*   [reduceRight (foldr)](http://underscorejs.org/#reduceRight)
*   [find (detect)](http://underscorejs.org/#find)
*   [findIndex](http://underscorejs.org/#findIndex)
*   [findLastIndex](http://underscorejs.org/#findLastIndex)
*   [filter (select)](http://underscorejs.org/#filter)
*   [reject](http://underscorejs.org/#reject)
*   [every (all)](http://underscorejs.org/#every)
*   [some (any)](http://underscorejs.org/#some)
*   [contains (includes)](http://underscorejs.org/#contains)
*   [invoke](http://underscorejs.org/#invoke)
*   [max](http://underscorejs.org/#max)
*   [min](http://underscorejs.org/#min)
*   [sortBy](http://underscorejs.org/#sortBy)
*   [groupBy](http://underscorejs.org/#groupBy)
*   [shuffle](http://underscorejs.org/#shuffle)
*   [toArray](http://underscorejs.org/#toArray)
*   [size](http://underscorejs.org/#size)
*   [first (head, take)](http://underscorejs.org/#first)
*   [initial](http://underscorejs.org/#initial)
*   [rest (tail, drop)](http://underscorejs.org/#rest)
*   [last](http://underscorejs.org/#last)
*   [without](http://underscorejs.org/#without)
*   [indexOf](http://underscorejs.org/#indexOf)
*   [lastIndexOf](http://underscorejs.org/#lastIndexOf)
*   [isEmpty](http://underscorejs.org/#isEmpty)
*   [chain](http://underscorejs.org/#chain)
*   [difference](http://underscorejs.org/#difference)
*   [sample](http://underscorejs.org/#sample)
*   [partition](http://underscorejs.org/#partition)
*   [countBy](http://underscorejs.org/#countBy)
*   [indexBy](http://underscorejs.org/#indexBy)

<pre>books.each(function(book) {
  book.publish();
});

var titles = books.map("title");

var publishedBooks = books.filter({published: true});

var alphabetical = books.sortBy(function(book) {
  return book.author.get("name").toLowerCase();
});

var randomThree = books.sample(3);
</pre>

**add**`collection.add(models, [options])`  
Add a model (or an array of models) to the collection, firing an <tt>"add"</tt> event for each model, and an <tt>"update"</tt> event afterwards. If a [model](#Collection-model) property is defined, you may also pass raw attributes objects, and have them be vivified as instances of the model. Returns the added (or preexisting, if duplicate) models. Pass <tt>{at: index}</tt> to splice the model into the collection at the specified <tt>index</tt>. If you're adding models to the collection that are _already_ in the collection, they'll be ignored, unless you pass <tt>{merge: true}</tt>, in which case their attributes will be merged into the corresponding models, firing any appropriate <tt>"change"</tt> events.

<pre class="runnable">var ships = new Backbone.Collection;

ships.on("add", function(ship) {
  alert("Ahoy " + ship.get("name") + "!");
});

ships.add([
  {name: "Flying Dutchman"},
  {name: "Black Pearl"}
]);
</pre>

Note that adding the same model (a model with the same <tt>id</tt>) to a collection more than once  
is a no-op.

**remove**`collection.remove(models, [options])`  
Remove a model (or an array of models) from the collection, and return them. Each model can be a Model instance, an <tt>id</tt> string or a JS object, any value acceptable as the <tt>id</tt> argument of [<tt>collection.get</tt>](#Collection-get). Fires a <tt>"remove"</tt> event for each model, and a single <tt>"update"</tt> event afterwards, unless <tt>{silent: true}</tt> is passed. The model's index before removal is available to listeners as <tt>options.index</tt>.

**reset**`collection.reset([models], [options])`  
Adding and removing models one at a time is all well and good, but sometimes you have so many models to change that you'd rather just update the collection in bulk. Use **reset** to replace a collection with a new list of models (or attribute hashes), triggering a single <tt>"reset"</tt> event on completion, and _without_ triggering any add or remove events on any models. Returns the newly-set models. For convenience, within a <tt>"reset"</tt> event, the list of any previous models is available as <tt>options.previousModels</tt>.  
Pass <tt>null</tt> for <tt>models</tt> to empty your Collection with <tt>options</tt>.

Here's an example using **reset** to bootstrap a collection during initial page load, in a Rails application:

<pre><script>
  var accounts = new Backbone.Collection;
  accounts.reset(<%= @accounts.to_json %>);
</script>
</pre>

Calling <tt>collection.reset()</tt> without passing any models as arguments will empty the entire collection.

**set**`collection.set(models, [options])`  
The **set** method performs a "smart" update of the collection with the passed list of models. If a model in the list isn't yet in the collection it will be added; if the model is already in the collection its attributes will be merged; and if the collection contains any models that _aren't_ present in the list, they'll be removed. All of the appropriate <tt>"add"</tt>, <tt>"remove"</tt>, and <tt>"change"</tt> events are fired as this happens. Returns the touched models in the collection. If you'd like to customize the behavior, you can disable it with options: <tt>{add: false}</tt>, <tt>{remove: false}</tt>, or <tt>{merge: false}</tt>.

<pre>var vanHalen = new Backbone.Collection([eddie, alex, stone, roth]);

vanHalen.set([eddie, alex, stone, hagar]);

// Fires a "remove" event for roth, and an "add" event for "hagar".
// Updates any of stone, alex, and eddie's attributes that may have
// changed over the years.
</pre>

**get**`collection.get(id)`  
Get a model from a collection, specified by an [id](#Model-id), a [cid](#Model-cid), or by passing in a **model**.

<pre>var book = library.get(110);
</pre>

**at**`collection.at(index)`  
Get a model from a collection, specified by index. Useful if your collection is sorted, and if your collection isn't sorted, **at** will still retrieve models in insertion order. When passed a negative index, it will retrieve the model from the back of the collection.

**push**`collection.push(model, [options])`  
Add a model at the end of a collection. Takes the same options as [add](#Collection-add).

**pop**`collection.pop([options])`  
Remove and return the last model from a collection. Takes the same options as [remove](#Collection-remove).

**unshift**`collection.unshift(model, [options])`  
Add a model at the beginning of a collection. Takes the same options as [add](#Collection-add).

**shift**`collection.shift([options])`  
Remove and return the first model from a collection. Takes the same options as [remove](#Collection-remove).

**slice**`collection.slice(begin, end)`  
Return a shallow copy of this collection's models, using the same options as native [Array#slice](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/slice).

**length**`collection.length`  
Like an array, a Collection maintains a <tt>length</tt> property, counting the number of models it contains.

**comparator**`collection.comparator`  
By default there is no **comparator** for a collection. If you define a comparator, it will be used to maintain the collection in sorted order. This means that as models are added, they are inserted at the correct index in <tt>collection.models</tt>. A comparator can be defined as a [sortBy](http://underscorejs.org/#sortBy) (pass a function that takes a single argument), as a [sort](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) (pass a comparator function that expects two arguments), or as a string indicating the attribute to sort by.

"sortBy" comparator functions take a model and return a numeric or string value by which the model should be ordered relative to others. "sort" comparator functions take two models, and return <tt>-1</tt> if the first model should come before the second, <tt>0</tt> if they are of the same rank and <tt>1</tt> if the first model should come after. _Note that Backbone depends on the arity of your comparator function to determine between the two styles, so be careful if your comparator function is bound._

Note how even though all of the chapters in this example are added backwards, they come out in the proper order:

<pre class="runnable">var Chapter  = Backbone.Model;
var chapters = new Backbone.Collection;

chapters.comparator = 'page';

chapters.add(new Chapter({page: 9, title: "The End"}));
chapters.add(new Chapter({page: 5, title: "The Middle"}));
chapters.add(new Chapter({page: 1, title: "The Beginning"}));

alert(chapters.pluck('title'));
</pre>

Collections with a comparator will not automatically re-sort if you later change model attributes, so you may wish to call <tt>sort</tt> after changing model attributes that would affect the order.

**sort**`collection.sort([options])`  
Force a collection to re-sort itself. You don't need to call this under normal circumstances, as a collection with a [comparator](#Collection-comparator) will sort itself whenever a model is added. To disable sorting when adding a model, pass <tt>{sort: false}</tt> to <tt>add</tt>. Calling **sort** triggers a <tt>"sort"</tt> event on the collection.

**pluck**`collection.pluck(attribute)`  
Pluck an attribute from each model in the collection. Equivalent to calling <tt>map</tt> and returning a single attribute from the iterator.

<pre class="runnable">var stooges = new Backbone.Collection([
  {name: "Curly"},
  {name: "Larry"},
  {name: "Moe"}
]);

var names = stooges.pluck("name");

alert(JSON.stringify(names));
</pre>

**where**`collection.where(attributes)`  
Return an array of all the models in a collection that match the passed **attributes**. Useful for simple cases of <tt>filter</tt>.

<pre class="runnable">var friends = new Backbone.Collection([
  {name: "Athos",      job: "Musketeer"},
  {name: "Porthos",    job: "Musketeer"},
  {name: "Aramis",     job: "Musketeer"},
  {name: "d'Artagnan", job: "Guard"},
]);

var musketeers = friends.where({job: "Musketeer"});

alert(musketeers.length);
</pre>

**findWhere**`collection.findWhere(attributes)`  
Just like [where](#Collection-where), but directly returns only the first model in the collection that matches the passed **attributes**. If no model matches returns <tt>undefined</tt>.

**url**`collection.url or collection.url()`  
Set the **url** property (or function) on a collection to reference its location on the server. Models within the collection will use **url** to construct URLs of their own.

<pre>var Notes = Backbone.Collection.extend({
  url: '/notes'
});

// Or, something more sophisticated:

var Notes = Backbone.Collection.extend({
  url: function() {
    return this.document.url() + '/notes';
  }
});
</pre>

**parse**`collection.parse(response, options)`  
**parse** is called by Backbone whenever a collection's models are returned by the server, in [fetch](#Collection-fetch). The function is passed the raw <tt>response</tt> object, and should return the array of model attributes to be [added](#Collection-add) to the collection. The default implementation is a no-op, simply passing through the JSON response. Override this if you need to work with a preexisting API, or better namespace your responses.

<pre>var Tweets = Backbone.Collection.extend({
  // The Twitter Search API returns tweets under "results".
  parse: function(response) {
    return response.results;
  }
});
</pre>

**clone**`collection.clone()`  
Returns a new instance of the collection with an identical list of models.

**fetch**`collection.fetch([options])`  
Fetch the default set of models for this collection from the server, [setting](#Collection-set) them on the collection when they arrive. The **options** hash takes <tt>success</tt> and <tt>error</tt> callbacks which will both be passed <tt>(collection, response, options)</tt> as arguments. When the model data returns from the server, it uses [set](#Collection-set) to (intelligently) merge the fetched models, unless you pass <tt>{reset: true}</tt>, in which case the collection will be (efficiently) [reset](#Collection-reset). Delegates to [Backbone.sync](#Sync) under the covers for custom persistence strategies and returns a [jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR). The server handler for **fetch** requests should return a JSON array of models.

<pre class="runnable">Backbone.sync = function(method, model) {
  alert(method + ": " + model.url);
};

var accounts = new Backbone.Collection;
accounts.url = '/accounts';

accounts.fetch();
</pre>

The behavior of **fetch** can be customized by using the available [set](#Collection-set) options. For example, to fetch a collection, getting an <tt>"add"</tt> event for every new model, and a <tt>"change"</tt> event for every changed existing model, without removing anything: <tt>collection.fetch({remove: false})</tt>

**jQuery.ajax** options can also be passed directly as **fetch** options, so to fetch a specific page of a paginated collection: <tt>Documents.fetch({data: {page: 3}})</tt>

Note that **fetch** should not be used to populate collections on page load — all models needed at load time should already be [bootstrapped](#FAQ-bootstrap) in to place. **fetch** is intended for lazily-loading models for interfaces that are not needed immediately: for example, documents with collections of notes that may be toggled open and closed.

**create**`collection.create(attributes, [options])`  
Convenience to create a new instance of a model within a collection. Equivalent to instantiating a model with a hash of attributes, saving the model to the server, and adding the model to the set after being successfully created. Returns the new model. If client-side validation failed, the model will be unsaved, with validation errors. In order for this to work, you should set the [model](#Collection-model) property of the collection. The **create** method can accept either an attributes hash or an existing, unsaved model object.

Creating a model will cause an immediate <tt>"add"</tt> event to be triggered on the collection, a <tt>"request"</tt> event as the new model is sent to the server, as well as a <tt>"sync"</tt> event, once the server has responded with the successful creation of the model. Pass <tt>{wait: true}</tt> if you'd like to wait for the server before adding the new model to the collection.

<pre>var Library = Backbone.Collection.extend({
  model: Book
});

var nypl = new Library;

var othello = nypl.create({
  title: "Othello",
  author: "William Shakespeare"
});
</pre>

## Backbone.Router

Web applications often provide linkable, bookmarkable, shareable URLs for important locations in the app. Until recently, hash fragments (<tt>#page</tt>) were used to provide these permalinks, but with the arrival of the History API, it's now possible to use standard URLs (<tt>/page</tt>). **Backbone.Router** provides methods for routing client-side pages, and connecting them to actions and events. For browsers which don't yet support the History API, the Router handles graceful fallback and transparent translation to the fragment version of the URL.

During page load, after your application has finished creating all of its routers, be sure to call <tt>Backbone.history.start()</tt> or <tt>Backbone.history.start({pushState: true})</tt> to route the initial URL.

**extend**`Backbone.Router.extend(properties, [classProperties])`  
Get started by creating a custom router class. Define actions that are triggered when certain URL fragments are matched, and provide a [routes](#Router-routes) hash that pairs routes to actions. Note that you'll want to avoid using a leading slash in your route definitions:

<pre>var Workspace = Backbone.Router.extend({

  routes: {
    "help":                 "help",    // #help
    "search/:query":        "search",  // #search/kiwis
    "search/:query/p:page": "search"   // #search/kiwis/p7
  },

  help: function() {
    ...
  },

  search: function(query, page) {
    ...
  }

});
</pre>

**routes**`router.routes`  
The routes hash maps URLs with parameters to functions on your router (or just direct function definitions, if you prefer), similar to the [View](#View)'s [events hash](#View-delegateEvents). Routes can contain parameter parts, <tt>:param</tt>, which match a single URL component between slashes; and splat parts <tt>*splat</tt>, which can match any number of URL components. Part of a route can be made optional by surrounding it in parentheses <tt>(/:optional)</tt>.

For example, a route of <tt>"search/:query/p:page"</tt> will match a fragment of <tt>#search/obama/p2</tt>, passing <tt>"obama"</tt> and <tt>"2"</tt> to the action.

A route of <tt>"file/*path"</tt> will match <tt>#file/folder/file.txt</tt>, passing <tt>"folder/file.txt"</tt> to the action.

A route of <tt>"docs/:section(/:subsection)"</tt> will match <tt>#docs/faq</tt> and <tt>#docs/faq/installing</tt>, passing <tt>"faq"</tt> to the action in the first case, and passing <tt>"faq"</tt> and <tt>"installing"</tt> to the action in the second.

A nested optional route of <tt>"docs(/:section)(/:subsection)"</tt> will match <tt>#docs</tt>, <tt>#docs/faq</tt>, and <tt>#docs/faq/installing</tt>, passing <tt>"faq"</tt> to the action in the second case, and passing <tt>"faq"</tt> and <tt>"installing"</tt> to the action in the third.

Trailing slashes are treated as part of the URL, and (correctly) treated as a unique route when accessed. <tt>docs</tt> and <tt>docs/</tt> will fire different callbacks. If you can't avoid generating both types of URLs, you can define a <tt>"docs(/)"</tt> matcher to capture both cases.

When the visitor presses the back button, or enters a URL, and a particular route is matched, the name of the action will be fired as an [event](#Events), so that other objects can listen to the router, and be notified. In the following example, visiting <tt>#help/uploading</tt> will fire a <tt>route:help</tt> event from the router.

<pre>routes: {
  "help/:page":         "help",
  "download/*path":     "download",
  "folder/:name":       "openFolder",
  "folder/:name-:mode": "openFolder"
}
</pre>

<pre>router.on("route:help", function(page) {
  ...
});
</pre>

**constructor / initialize**`new Router([options])`  
When creating a new router, you may pass its [routes](#Router-routes) hash directly as an option, if you choose. All <tt>options</tt> will also be passed to your <tt>initialize</tt> function, if defined.

**route**`router.route(route, name, [callback])`  
Manually create a route for the router, The <tt>route</tt> argument may be a [routing string](#Router-routes) or regular expression. Each matching capture from the route or regular expression will be passed as an argument to the callback. The <tt>name</tt> argument will be triggered as a <tt>"route:name"</tt> event whenever the route is matched. If the <tt>callback</tt> argument is omitted <tt>router[name]</tt> will be used instead. Routes added later may override previously declared routes.

<pre>initialize: function(options) {

  // Matches #page/10, passing "10"
  this.route("page/:number", "page", function(number){ ... });

  // Matches /117-a/b/c/open, passing "117-a/b/c" to this.open
  this.route(/^(.*?)\/open$/, "open");

},

open: function(id) { ... }
</pre>

**navigate**`router.navigate(fragment, [options])`  
Whenever you reach a point in your application that you'd like to save as a URL, call **navigate** in order to update the URL. If you also wish to call the route function, set the **trigger** option to <tt>true</tt>. To update the URL without creating an entry in the browser's history, set the **replace** option to <tt>true</tt>.

<pre>openPage: function(pageNumber) {
  this.document.pages.at(pageNumber).open();
  this.navigate("page/" + pageNumber);
}

# Or ...

app.navigate("help/troubleshooting", {trigger: true});

# Or ...

app.navigate("help/troubleshooting", {trigger: true, replace: true});
</pre>

**execute**`router.execute(callback, args, name)`  
This method is called internally within the router, whenever a route matches and its corresponding **callback** is about to be executed. Return **false** from execute to cancel the current transition. Override it to perform custom parsing or wrapping of your routes, for example, to parse query strings before handing them to your route callback, like so:

<pre>var Router = Backbone.Router.extend({
  execute: function(callback, args, name) {
    if (!loggedIn) {
      goToLogin();
      return false;
    }
    args.push(parseQueryString(args.pop()));
    if (callback) callback.apply(this, args);
  }
});
</pre>

## Backbone.history

**History** serves as a global router (per frame) to handle <tt>hashchange</tt> events or <tt>pushState</tt>, match the appropriate route, and trigger callbacks. You shouldn't ever have to create one of these yourself since <tt>Backbone.history</tt> already contains one.

**pushState** support exists on a purely opt-in basis in Backbone. Older browsers that don't support <tt>pushState</tt> will continue to use hash-based URL fragments, and if a hash URL is visited by a <tt>pushState</tt>-capable browser, it will be transparently upgraded to the true URL. Note that using real URLs requires your web server to be able to correctly render those pages, so back-end changes are required as well. For example, if you have a route of <tt>/documents/100</tt>, your web server must be able to serve that page, if the browser visits that URL directly. For full search-engine crawlability, it's best to have the server generate the complete HTML for the page ... but if it's a web application, just rendering the same content you would have for the root URL, and filling in the rest with Backbone Views and JavaScript works fine.

**start**`Backbone.history.start([options])`  
When all of your [Routers](#Router) have been created, and all of the routes are set up properly, call <tt>Backbone.history.start()</tt> to begin monitoring <tt>hashchange</tt> events, and dispatching routes. Subsequent calls to <tt>Backbone.history.start()</tt> will throw an error, and <tt>Backbone.History.started</tt> is a boolean value indicating whether it has already been called.

To indicate that you'd like to use HTML5 <tt>pushState</tt> support in your application, use <tt>Backbone.history.start({pushState: true})</tt>. If you'd like to use <tt>pushState</tt>, but have browsers that don't support it natively use full page refreshes instead, you can add <tt>{hashChange: false}</tt> to the options.

If your application is not being served from the root url <tt>/</tt> of your domain, be sure to tell History where the root really is, as an option: <tt>Backbone.history.start({pushState: true, root: "/public/search/"})</tt>

When called, if a route succeeds with a match for the current URL, <tt>Backbone.history.start()</tt> returns <tt>true</tt>. If no defined route matches the current URL, it returns <tt>false</tt>.

If the server has already rendered the entire page, and you don't want the initial route to trigger when starting History, pass <tt>silent: true</tt>.

Because hash-based history in Internet Explorer relies on an <tt><iframe></tt>, be sure to call <tt>start()</tt> only after the DOM is ready.

<pre>$(function(){
  new WorkspaceRouter();
  new HelpPaneRouter();
  Backbone.history.start({pushState: true});
});
</pre>

## Backbone.sync

**Backbone.sync** is the function that Backbone calls every time it attempts to read or save a model to the server. By default, it uses <tt>jQuery.ajax</tt> to make a RESTful JSON request and returns a [jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR). You can override it in order to use a different persistence strategy, such as WebSockets, XML transport, or Local Storage.

The method signature of **Backbone.sync** is <tt>sync(method, model, [options])</tt>

*   **method** – the CRUD method (<tt>"create"</tt>, <tt>"read"</tt>, <tt>"update"</tt>, or <tt>"delete"</tt>)
*   **model** – the model to be saved (or collection to be read)
*   **options** – success and error callbacks, and all other jQuery request options

With the default implementation, when **Backbone.sync** sends up a request to save a model, its attributes will be passed, serialized as JSON, and sent in the HTTP body with content-type <tt>application/json</tt>. When returning a JSON response, send down the attributes of the model that have been changed by the server, and need to be updated on the client. When responding to a <tt>"read"</tt> request from a collection ([Collection#fetch](#Collection-fetch)), send down an array of model attribute objects.

Whenever a model or collection begins a **sync** with the server, a <tt>"request"</tt> event is emitted. If the request completes successfully you'll get a <tt>"sync"</tt> event, and an <tt>"error"</tt> event if not.

The **sync** function may be overridden globally as <tt>Backbone.sync</tt>, or at a finer-grained level, by adding a <tt>sync</tt> function to a Backbone collection or to an individual model.

The default **sync** handler maps CRUD to REST like so:

*   **create → POST  ** <tt>/collection</tt>
*   **read → GET  ** <tt>/collection[/id]</tt>
*   **update → PUT  ** <tt>/collection/id</tt>
*   **patch → PATCH  ** <tt>/collection/id</tt>
*   **delete → DELETE  ** <tt>/collection/id</tt>

As an example, a Rails 4 handler responding to an <tt>"update"</tt> call from <tt>Backbone</tt> might look like this:

<pre>def update
  account = Account.find params[:id]
  permitted = params.require(:account).permit(:name, :otherparam)
  account.update_attributes permitted
  render :json => account
end
</pre>

One more tip for integrating Rails versions prior to 3.1 is to disable the default namespacing for <tt>to_json</tt> calls on models by setting <tt>ActiveRecord::Base.include_root_in_json = false</tt>

**ajax**`Backbone.ajax = function(request) { ... };`  
If you want to use a custom AJAX function, or your endpoint doesn't support the [jQuery.ajax](http://api.jquery.com/jQuery.ajax/) API and you need to tweak things, you can do so by setting <tt>Backbone.ajax</tt>.

**emulateHTTP**`Backbone.emulateHTTP = true`  
If you want to work with a legacy web server that doesn't support Backbone's default REST/HTTP approach, you may choose to turn on <tt>Backbone.emulateHTTP</tt>. Setting this option will fake <tt>PUT</tt>, <tt>PATCH</tt> and <tt>DELETE</tt> requests with a HTTP <tt>POST</tt>, setting the <tt>X-HTTP-Method-Override</tt> header with the true method. If <tt>emulateJSON</tt> is also on, the true method will be passed as an additional <tt>_method</tt> parameter.

<pre>Backbone.emulateHTTP = true;

model.save();  // POST to "/collection/id", with "_method=PUT" + header.
</pre>

**emulateJSON**`Backbone.emulateJSON = true`  
If you're working with a legacy web server that can't handle requests encoded as <tt>application/json</tt>, setting <tt>Backbone.emulateJSON = true;</tt> will cause the JSON to be serialized under a <tt>model</tt> parameter, and the request to be made with a <tt>application/x-www-form-urlencoded</tt> MIME type, as if from an HTML form.

## Backbone.View

Backbone views are almost more convention than they are code — they don't determine anything about your HTML or CSS for you, and can be used with any JavaScript templating library. The general idea is to organize your interface into logical views, backed by models, each of which can be updated independently when the model changes, without having to redraw the page. Instead of digging into a JSON object, looking up an element in the DOM, and updating the HTML by hand, you can bind your view's <tt>render</tt> function to the model's <tt>"change"</tt> event — and now everywhere that model data is displayed in the UI, it is always immediately up to date.

**extend**`Backbone.View.extend(properties, [classProperties])`  
Get started with views by creating a custom view class. You'll want to override the [render](#View-render) function, specify your declarative [events](#View-delegateEvents), and perhaps the <tt>tagName</tt>, <tt>className</tt>, or <tt>id</tt> of the View's root element.

<pre>var DocumentRow = Backbone.View.extend({

  tagName: "li",

  className: "document-row",

  events: {
    "click .icon":          "open",
    "click .button.edit":   "openEditDialog",
    "click .button.delete": "destroy"
  },

  initialize: function() {
    this.listenTo(this.model, "change", this.render);
  },

  render: function() {
    ...
  }

});
</pre>

Properties like <tt>tagName</tt>, <tt>id</tt>, <tt>className</tt>, <tt>el</tt>, and <tt>events</tt> may also be defined as a function, if you want to wait to define them until runtime.

**constructor / initialize**`new View([options])`  
There are several special options that, if passed, will be attached directly to the view: <tt>model</tt>, <tt>collection</tt>, <tt>el</tt>, <tt>id</tt>, <tt>className</tt>, <tt>tagName</tt>, <tt>attributes</tt> and <tt>events</tt>. If the view defines an **initialize** function, it will be called when the view is first created. If you'd like to create a view that references an element _already_ in the DOM, pass in the element as an option: <tt>new View({el: existingElement})</tt>

<pre>var doc = documents.first();

new DocumentRow({
  model: doc,
  id: "document-row-" + doc.id
});
</pre>

**el**`view.el`  
All views have a DOM element at all times (the **el** property), whether they've already been inserted into the page or not. In this fashion, views can be rendered at any time, and inserted into the DOM all at once, in order to get high-performance UI rendering with as few reflows and repaints as possible.

<tt>this.el</tt> can be resolved from a DOM selector string or an Element; otherwise it will be created from the view's <tt>tagName</tt>, <tt>className</tt>, <tt>id</tt> and [<tt>attributes</tt>](#View-attributes) properties. If none are set, <tt>this.el</tt> is an empty <tt>div</tt>, which is often just fine. An **el** reference may also be passed in to the view's constructor.

<pre class="runnable">var ItemView = Backbone.View.extend({
  tagName: 'li'
});

var BodyView = Backbone.View.extend({
  el: 'body'
});

var item = new ItemView();
var body = new BodyView();

alert(item.el + ' ' + body.el);
</pre>

**$el**`view.$el`  
A cached jQuery object for the view's element. A handy reference instead of re-wrapping the DOM element all the time.

<pre>view.$el.show();

listView.$el.append(itemView.el);
</pre>

**setElement**`view.setElement(element)`  
If you'd like to apply a Backbone view to a different DOM element, use **setElement**, which will also create the cached <tt>$el</tt> reference and move the view's delegated events from the old element to the new one.

**attributes**`view.attributes`  
A hash of attributes that will be set as HTML DOM element attributes on the view's <tt>el</tt> (id, class, data-properties, etc.), or a function that returns such a hash.

**$ (jQuery)**`view.$(selector)`  
If jQuery is included on the page, each view has a **$** function that runs queries scoped within the view's element. If you use this scoped jQuery function, you don't have to use model ids as part of your query to pull out specific elements in a list, and can rely much more on HTML class attributes. It's equivalent to running: <tt>view.$el.find(selector)</tt>

<pre>ui.Chapter = Backbone.View.extend({
  serialize : function() {
    return {
      title: this.$(".title").text(),
      start: this.$(".start-page").text(),
      end:   this.$(".end-page").text()
    };
  }
});
</pre>

**template**`view.template([data])`  
While templating for a view isn't a function provided directly by Backbone, it's often a nice convention to define a **template** function on your views. In this way, when rendering your view, you have convenient access to instance data. For example, using Underscore templates:

<pre>var LibraryView = Backbone.View.extend({
  template: _.template(...)
});
</pre>

**render**`view.render()`  
The default implementation of **render** is a no-op. Override this function with your code that renders the view template from model data, and updates <tt>this.el</tt> with the new HTML. A good convention is to <tt>return this</tt> at the end of **render** to enable chained calls.

<pre>var Bookmark = Backbone.View.extend({
  template: _.template(...),
  render: function() {
    this.$el.html(this.template(this.model.attributes));
    return this;
  }
});
</pre>

Backbone is agnostic with respect to your preferred method of HTML templating. Your **render** function could even munge together an HTML string, or use <tt>document.createElement</tt> to generate a DOM tree. However, we suggest choosing a nice JavaScript templating library. [Mustache.js](http://github.com/janl/mustache.js), [Haml-js](http://github.com/creationix/haml-js), and [Eco](http://github.com/sstephenson/eco) are all fine alternatives. Because [Underscore.js](http://underscorejs.org/) is already on the page, [_.template](http://underscorejs.org/#template) is available, and is an excellent choice if you prefer simple interpolated-JavaScript style templates.

Whatever templating strategy you end up with, it's nice if you _never_ have to put strings of HTML in your JavaScript. At DocumentCloud, we use [Jammit](http://documentcloud.github.com/jammit/) in order to package up JavaScript templates stored in <tt>/app/views</tt> as part of our main <tt>core.js</tt> asset package.

**remove**`view.remove()`  
Removes a view and its <tt>el</tt> from the DOM, and calls [stopListening](#Events-stopListening) to remove any bound events that the view has [listenTo](#Events-listenTo)'d.

**events**`view.events or view.events()`  
The **events** hash (or method) can be used to specify a set of DOM events that will be bound to methods on your View through [delegateEvents](#View-delegateEvents).

Backbone will automatically attach the event listeners at instantiation time, right before invoking [initialize](#View-constructor).

<pre>var ENTER_KEY = 13;
var InputView = Backbone.View.extend({

  tagName: 'input',

  events: {
    "keydown" : "keyAction",
  },

  render: function() { ... },

  keyAction: function(e) {
    if (e.which === ENTER_KEY) {
      this.collection.add({text: this.$el.val()});
    }
  }
});
</pre>

**delegateEvents**`delegateEvents([events])`  
Uses jQuery's <tt>on</tt> function to provide declarative callbacks for DOM events within a view. If an **events** hash is not passed directly, uses <tt>this.events</tt> as the source. Events are written in the format <tt>{"event selector": "callback"}</tt>. The callback may be either the name of a method on the view, or a direct function body. Omitting the <tt>selector</tt> causes the event to be bound to the view's root element (<tt>this.el</tt>). By default, <tt>delegateEvents</tt> is called within the View's constructor for you, so if you have a simple <tt>events</tt> hash, all of your DOM events will always already be connected, and you will never have to call this function yourself.

The <tt>events</tt> property may also be defined as a function that returns an **events** hash, to make it easier to programmatically define your events, as well as inherit them from parent views.

Using **delegateEvents** provides a number of advantages over manually using jQuery to bind events to child elements during [render](#View-render). All attached callbacks are bound to the view before being handed off to jQuery, so when the callbacks are invoked, <tt>this</tt> continues to refer to the view object. When **delegateEvents** is run again, perhaps with a different <tt>events</tt> hash, all callbacks are removed and delegated afresh — useful for views which need to behave differently when in different modes.

A single-event version of **delegateEvents** is available as <tt>delegate</tt>. In fact, **delegateEvents** is simply a multi-event wrapper around <tt>delegate</tt>. A counterpart to <tt>undelegateEvents</tt> is available as <tt>undelegate</tt>.

A view that displays a document in a search result might look something like this:

<pre>var DocumentView = Backbone.View.extend({

  events: {
    "dblclick"                : "open",
    "click .icon.doc"         : "select",
    "contextmenu .icon.doc"   : "showMenu",
    "click .show_notes"       : "toggleNotes",
    "click .title .lock"      : "editAccessLevel",
    "mouseover .title .date"  : "showTooltip"
  },

  render: function() {
    this.$el.html(this.template(this.model.attributes));
    return this;
  },

  open: function() {
    window.open(this.model.get("viewer_url"));
  },

  select: function() {
    this.model.set({selected: true});
  },

  ...

});
</pre>

**undelegateEvents**`undelegateEvents()`  
Removes all of the view's delegated events. Useful if you want to disable or remove a view from the DOM temporarily.

## Utility

**Backbone.noConflict**`var backbone = Backbone.noConflict();`  
Returns the <tt>Backbone</tt> object back to its original value. You can use the return value of <tt>Backbone.noConflict()</tt> to keep a local reference to Backbone. Useful for embedding Backbone on third-party websites, where you don't want to clobber the existing Backbone.

<pre>var localBackbone = Backbone.noConflict();
var model = localBackbone.Model.extend(...);
</pre>

**Backbone.$**`Backbone.$ = $;`  
If you have multiple copies of <tt>jQuery</tt> on the page, or simply want to tell Backbone to use a particular object as its DOM / Ajax library, this is the property for you.

<pre>Backbone.$ = require('jquery');
</pre>

## F.A.Q.

**Why use Backbone, not [other framework X]?**  
If your eye hasn't already been caught by the adaptability and elan on display in the above [list of examples](#examples), we can get more specific: Backbone.js aims to provide the common foundation that data-rich web applications with ambitious interfaces require — while very deliberately avoiding painting you into a corner by making any decisions that you're better equipped to make yourself.

*   The focus is on supplying you with [helpful methods to manipulate and query your data](#Collection-Underscore-Methods), not on HTML widgets or reinventing the JavaScript object model.
*   Backbone does not force you to use a single template engine. Views can bind to HTML constructed in [your](http://underscorejs.org/#template) [favorite](http://guides.rubyonrails.org/layouts_and_rendering.html) [way](http://mustache.github.com).
*   It's smaller. There are fewer kilobytes for your browser or phone to download, and less _conceptual_ surface area. You can read and understand the source in an afternoon.
*   It doesn't depend on stuffing application logic into your HTML. There's no embedded JavaScript, template logic, or binding hookup code in <tt>data-</tt> or <tt>ng-</tt> attributes, and no need to invent your own HTML tags.
*   [Synchronous events](#Events) are used as the fundamental building block, not a difficult-to-reason-about run loop, or by constantly polling and traversing your data structures to hunt for changes. And if you want a specific event to be asynchronous and aggregated, [no problem](http://underscorejs.org/#debounce).
*   Backbone scales well, from [embedded widgets](http://disqus.com) to [massive apps](http://www.usatoday.com).
*   Backbone is a library, not a framework, and plays well with others. You can embed Backbone widgets in Dojo apps without trouble, or use Backbone models as the data backing for D3 visualizations (to pick two entirely random examples).
*   "Two-way data-binding" is avoided. While it certainly makes for a nifty demo, and works for the most basic CRUD, it doesn't tend to be terribly useful in your real-world app. Sometimes you want to update on every keypress, sometimes on blur, sometimes when the panel is closed, and sometimes when the "save" button is clicked. In almost all cases, simply serializing the form to JSON is faster and easier. All that aside, if your heart is set, [go](http://rivetsjs.com) [for it](http://nytimes.github.com/backbone.stickit/).
*   There's no built-in performance penalty for choosing to structure your code with Backbone. And if you do want to optimize further, thin models and templates with flexible granularity make it easy to squeeze every last drop of potential performance out of, say, IE8.

**There's More Than One Way To Do It**  
It's common for folks just getting started to treat the examples listed on this page as some sort of gospel truth. In fact, Backbone.js is intended to be fairly agnostic about many common patterns in client-side code. For example...

**References between Models and Views** can be handled several ways. Some people like to have direct pointers, where views correspond 1:1 with models (<tt>model.view</tt> and <tt>view.model</tt>). Others prefer to have intermediate "controller" objects that orchestrate the creation and organization of views into a hierarchy. Others still prefer the evented approach, and always fire events instead of calling methods directly. All of these styles work well.

**Batch operations** on Models are common, but often best handled differently depending on your server-side setup. Some folks don't mind making individual Ajax requests. Others create explicit resources for RESTful batch operations: <tt>/notes/batch/destroy?ids=1,2,3,4</tt>. Others tunnel REST over JSON, with the creation of "changeset" requests:

<pre>  {
    "create":  [array of models to create]
    "update":  [array of models to update]
    "destroy": [array of model ids to destroy]
  }
</pre>

**Feel free to define your own events.** [Backbone.Events](#Events) is designed so that you can mix it in to any JavaScript object or prototype. Since you can use any string as an event, it's often handy to bind and trigger your own custom events: <tt>model.on("selected:true")</tt> or <tt>model.on("editing")</tt>

**Render the UI** as you see fit. Backbone is agnostic as to whether you use [Underscore templates](http://underscorejs.org/#template), [Mustache.js](https://github.com/janl/mustache.js), direct DOM manipulation, server-side rendered snippets of HTML, or [jQuery UI](http://jqueryui.com/) in your <tt>render</tt> function. Sometimes you'll create a view for each model ... sometimes you'll have a view that renders thousands of models at once, in a tight loop. Both can be appropriate in the same app, depending on the quantity of data involved, and the complexity of the UI.

**Nested Models & Collections**  
It's common to nest collections inside of models with Backbone. For example, consider a <tt>Mailbox</tt> model that contains many <tt>Message</tt> models. One nice pattern for handling this is have a <tt>this.messages</tt> collection for each mailbox, enabling the lazy-loading of messages, when the mailbox is first opened ... perhaps with <tt>MessageList</tt> views listening for <tt>"add"</tt> and <tt>"remove"</tt> events.

<pre>var Mailbox = Backbone.Model.extend({

  initialize: function() {
    this.messages = new Messages;
    this.messages.url = '/mailbox/' + this.id + '/messages';
    this.messages.on("reset", this.updateCounts);
  },

  ...

});

var inbox = new Mailbox;

// And then, when the Inbox is opened:

inbox.messages.fetch({reset: true});
</pre>

If you're looking for something more opinionated, there are a number of Backbone plugins that add sophisticated associations among models, [available on the wiki](https://github.com/jashkenas/backbone/wiki/Extensions%2C-Plugins%2C-Resources).

Backbone doesn't include direct support for nested models and collections or "has many" associations because there are a number of good patterns for modeling structured data on the client side, and _Backbone should provide the foundation for implementing any of them._ You may want to…

*   Mirror an SQL database's structure, or the structure of a NoSQL database.
*   Use models with arrays of "foreign key" ids, and join to top level collections (a-la tables).
*   For associations that are numerous, use a range of ids instead of an explicit list.
*   Avoid ids, and use direct references, creating a partial object graph representing your data set.
*   Lazily load joined models from the server, or lazily deserialize nested models from JSON documents.

**Loading Bootstrapped Models**  
When your app first loads, it's common to have a set of initial models that you know you're going to need, in order to render the page. Instead of firing an extra AJAX request to [fetch](#Collection-fetch) them, a nicer pattern is to have their data already bootstrapped into the page. You can then use [reset](#Collection-reset) to populate your collections with the initial data. At DocumentCloud, in the [ERB](http://en.wikipedia.org/wiki/ERuby) template for the workspace, we do something along these lines:

<pre><script>
  var accounts = new Backbone.Collection;
  accounts.reset(<%= @accounts.to_json %>);
  var projects = new Backbone.Collection;
  projects.reset(<%= @projects.to_json(:collaborators => true) %>);
</script>
</pre>

You have to [escape](http://mathiasbynens.be/notes/etago) <tt></</tt> within the JSON string, to prevent javascript injection attacks.

**Extending Backbone**  
Many JavaScript libraries are meant to be insular and self-enclosed, where you interact with them by calling their public API, but never peek inside at the guts. Backbone.js is _not_ that kind of library.

Because it serves as a foundation for your application, you're meant to extend and enhance it in the ways you see fit — the entire source code is [annotated](docs/backbone.html) to make this easier for you. You'll find that there's very little there apart from core functions, and most of those can be overridden or augmented should you find the need. If you catch yourself adding methods to <tt>Backbone.Model.prototype</tt>, or creating your own base subclass, don't worry — that's how things are supposed to work.

**How does Backbone relate to "traditional" MVC?**  
Different implementations of the [Model-View-Controller](http://en.wikipedia.org/wiki/Model–View–Controller) pattern tend to disagree about the definition of a controller. If it helps any, in Backbone, the [View](#View) class can also be thought of as a kind of controller, dispatching events that originate from the UI, with the HTML template serving as the true view. We call it a View because it represents a logical chunk of UI, responsible for the contents of a single DOM element.

Comparing the overall structure of Backbone to a server-side MVC framework like **Rails**, the pieces line up like so:

*   **Backbone.Model** – Like a Rails model minus the class methods. Wraps a row of data in business logic.
*   **Backbone.Collection** – A group of models on the client-side, with sorting/filtering/aggregation logic.
*   **Backbone.Router** – Rails <tt>routes.rb</tt> + Rails controller actions. Maps URLs to functions.
*   **Backbone.View** – A logical, re-usable piece of UI. Often, but not always, associated with a model.
*   **Client-side Templates** – Rails <tt>.html.erb</tt> views, rendering a chunk of HTML.

**Binding "this"**  
Perhaps the single most common JavaScript "gotcha" is the fact that when you pass a function as a callback, its value for <tt>this</tt> is lost. When dealing with [events](#Events) and callbacks in Backbone, you'll often find it useful to rely on [listenTo](#Events-listenTo) or the optional <tt>context</tt> argument that many of Underscore and Backbone's methods use to specify the <tt>this</tt> that will be used when the callback is later invoked. (See [_.each](http://underscorejs.org/#each), [_.map](http://underscorejs.org/#map), and [object.on](#Events-on), to name a few). [View events](#View-delegateEvents) are automatically bound to the view's context for you. You may also find it helpful to use [_.bind](http://underscorejs.org/#bind) and [_.bindAll](http://underscorejs.org/#bindAll) from Underscore.js.

<pre>var MessageList = Backbone.View.extend({

  initialize: function() {
    var messages = this.collection;
    messages.on("reset", this.render, this);
    messages.on("add", this.addMessage, this);
    messages.on("remove", this.removeMessage, this);

    messsages.each(this.addMessage, this);
  }

});

// Later, in the app...

Inbox.messages.add(newMessage);
</pre>

**Working with Rails**  
Backbone.js was originally extracted from [a Rails application](http://www.documentcloud.org); getting your client-side (Backbone) Models to sync correctly with your server-side (Rails) Models is painless, but there are still a few things to be aware of.

By default, Rails versions prior to 3.1 add an extra layer of wrapping around the JSON representation of models. You can disable this wrapping by setting:

<pre>ActiveRecord::Base.include_root_in_json = false
</pre>

... in your configuration. Otherwise, override [parse](#Model-parse) to pull model attributes out of the wrapper. Similarly, Backbone PUTs and POSTs direct JSON representations of models, where by default Rails expects namespaced attributes. You can have your controllers filter attributes directly from <tt>params</tt>, or you can override [toJSON](#Model-toJSON) in Backbone to add the extra wrapping Rails expects.

## Examples

The list of examples that follows, while long, is not exhaustive. If you've worked on an app that uses Backbone, please add it to the [wiki page of Backbone apps](https://github.com/jashkenas/backbone/wiki/Projects-and-Companies-using-Backbone).

[Jérôme Gravel-Niquet](http://jgn.me/) has contributed a [Todo List application](examples/todos/index.html) that is bundled in the repository as Backbone example. If you're wondering where to get started with Backbone in general, take a moment to [read through the annotated source](docs/todos.html). The app uses a [LocalStorage adapter](http://github.com/jeromegn/Backbone.localStorage) to transparently save all of your todos within your browser, instead of sending them to a server. Jérôme also has a version hosted at [localtodos.com](http://localtodos.com/).

<div style="text-align: center;">[](examples/todos/index.html) </div>

## DocumentCloud

The [DocumentCloud workspace](http://www.documentcloud.org/public/#search/) is built on Backbone.js, with _Documents_, _Projects_, _Notes_, and _Accounts_ all as Backbone models and collections. If you're interested in history — both Underscore.js and Backbone.js were originally extracted from the DocumentCloud codebase, and packaged into standalone JS libraries.

<div style="text-align: center;">[](http://www.documentcloud.org/public/#search/) </div>

## USA Today

[USA Today](http://usatoday.com) takes advantage of the modularity of Backbone's data/model lifecycle — which makes it simple to create, inherit, isolate, and link application objects — to keep the codebase both manageable and efficient. The new website also makes heavy use of the Backbone Router to control the page for both pushState-capable and legacy browsers. Finally, the team took advantage of Backbone's Event module to create a PubSub API that allows third parties and analytics packages to hook into the heart of the app.

<div style="text-align: center;">[](http://usatoday.com) </div>

## Rdio

[New Rdio](http://rdio.com/new) was developed from the ground up with a component based framework based on Backbone.js. Every component on the screen is dynamically loaded and rendered, with data provided by the [Rdio API](http://developer.rdio.com/). When changes are pushed, every component can update itself without reloading the page or interrupting the user's music. All of this relies on Backbone's views and models, and all URL routing is handled by Backbone's Router. When data changes are signaled in realtime, Backbone's Events notify the interested components in the data changes. Backbone forms the core of the new, dynamic, realtime Rdio web and _desktop_ applications.

<div style="text-align: center;">[](http://rdio.com/new) </div>

## Hulu

[Hulu](http://hulu.com) used Backbone.js to build its next generation online video experience. With Backbone as a foundation, the web interface was rewritten from scratch so that all page content can be loaded dynamically with smooth transitions as you navigate. Backbone makes it easy to move through the app quickly without the reloading of scripts and embedded videos, while also offering models and collections for additional data manipulation support.

<div style="text-align: center;">[](http://hulu.com) </div>

## Quartz

[Quartz](http://qz.com) sees itself as a digitally native news outlet for the new global economy. Because Quartz believes in the future of open, cross-platform web applications, they selected Backbone and Underscore to fetch, sort, store, and display content from a custom WordPress API. Although [qz.com](http://qz.com) uses responsive design for phone, tablet, and desktop browsers, it also takes advantage of Backbone events and views to render device-specific templates in some cases.

<div style="text-align: center;">[](http://qz.com) </div>

## Earth

[Earth.nullschool.net](http://earth.nullschool.net) displays real-time weather conditions on an interactive animated globe, and Backbone provides the foundation upon which all of the site's components are built. Despite the presence of several other javascript libraries, Backbone's non-opinionated design made it effortless to mix-in the [Events](#Events) functionality used for distributing state changes throughout the page. When the decision was made to switch to Backbone, large blocks of custom logic simply disappeared.

<div style="text-align: center;">[](http://earth.nullschool.net) </div>

## Vox

Vox Media, the publisher of [SB Nation](http://www.sbnation.com/), [The Verge](http://www.theverge.com/), [Polygon](http://www.polygon.com/), [Eater](http://www.eater.com/), [Racked](http://www.racked.com/), [Curbed](http://www.curbed.com/), and [Vox.com](http://www.vox.com/), uses Backbone throughout [Chorus](http://techcrunch.com/2012/05/07/a-closer-look-at-chorus-the-next-generation-publishing-platform-that-runs-vox-media/), its home-grown publishing platform. Backbone powers the [liveblogging platform](http://product.voxmedia.com/post/25113965826/introducing-syllabus-vox-medias-s3-powered-liveblog) and [commenting system](http://product.voxmedia.com/2013/11/11/5426878/using-backbone-js-for-sanity-and-stability) used across all Vox Media properties; Coverage, an internal editorial coordination tool; [SB Nation Live](http://www.sbnation.com/college-basketball/2014/4/7/5592112/kentucky-vs-uconn-2014-ncaa-tournament-championship-live-chat), a live event coverage and chat tool; and [Vox Cards](http://www.vox.com/cards/ukraine-everything-you-need-to-know/what-is-the-ukraine-crisis), Vox.com's highlighter-and-index-card inspired app for providing context about the news.

<div style="text-align: center;">[](http://vox.com) </div>

## Gawker Media

[Kinja](http://kinja.com) is Gawker Media's publishing platform designed to create great stories by breaking down the lines between the traditional roles of content creators and consumers. Everyone — editors, readers, marketers — have access to the same tools to engage in passionate discussion and pursue the truth of the story. Sharing, recommending, and following within the Kinja ecosystem allows for improved information discovery across all the sites.

Kinja is the platform behind [Gawker](http://gawker.com/), [Gizmodo](http://gizmodo.com/), [Lifehacker](http://lifehacker.com/), [io9](http://io9.com/) and other Gawker Media blogs. Backbone.js underlies the front-end application code that powers everything from user authentication to post authoring, commenting, and even serving ads. The JavaScript stack includes [Underscore.js](http://underscorejs.org/) and [jQuery](http://jquery.com/), with some plugins, all loaded with [RequireJS](http://requirejs.org/). Closure templates are shared between the [Play! Framework](http://www.playframework.com/) based Scala application and Backbone views, and the responsive layout is done with the [Foundation](http://foundation.zurb.com/) framework using [SASS](http://sass-lang.com/).

<div style="text-align: center;">[](http://gawker.com) </div>

## Flow

[MetaLab](http://www.metalabdesign.com/) used Backbone.js to create [Flow](http://www.getflow.com/), a task management app for teams. The workspace relies on Backbone.js to construct task views, activities, accounts, folders, projects, and tags. You can see the internals under <tt>window.Flow</tt>.

<div style="text-align: center;">[](http://www.getflow.com/) </div>

## Gilt Groupe

[Gilt Groupe](http://gilt.com) uses Backbone.js to build multiple applications across their family of sites. [Gilt's mobile website](http://m.gilt.com) uses Backbone and [Zepto.js](http://zeptojs.com) to create a blazing-fast shopping experience for users on-the-go, while [Gilt Live](http://live.gilt.com) combines Backbone with WebSockets to display the items that customers are buying in real-time. Gilt's search functionality also uses Backbone to filter and sort products efficiently by moving those actions to the client-side.

<div style="text-align: center;">[](http://www.gilt.com/) </div>

## Enigma

[Enigma](http://enigma.io) is a portal amassing the largest collection of public data produced by governments, universities, companies, and organizations. Enigma uses Backbone Models and Collections to represent complex data structures; and Backbone's Router gives Enigma users unique URLs for application states, allowing them to navigate quickly through the site while maintaining the ability to bookmark pages and navigate forward and backward through their session.

<div style="text-align: center;">[](http://www.enigma.io/) </div>

## NewsBlur

[NewsBlur](http://www.newsblur.com) is an RSS feed reader and social news network with a fast and responsive UI that feels like a native desktop app. Backbone.js was selected for [a major rewrite and transition from spaghetti code](http://www.ofbrooklyn.com/2012/11/13/backbonification-migrating-javascript-to-backbone/) because of its powerful yet simple feature set, easy integration, and large community. If you want to poke around under the hood, NewsBlur is also entirely [open-source](http://github.com/samuelclay/NewsBlur).

<div style="text-align: center;">[](http://newsblur.com) </div>

## WordPress.com

[WordPress.com](http://wordpress.com/) is the software-as-a-service version of [WordPress](http://wordpress.org). It uses Backbone.js Models, Collections, and Views in its [Notifications system](http://en.blog.wordpress.com/2012/05/25/notifications-refreshed/). Backbone.js was selected because it was easy to fit into the structure of the application, not the other way around. [Automattic](http://automattic.com) (the company behind WordPress.com) is integrating Backbone.js into the Stats tab and other features throughout the homepage.

<div style="text-align: center;">[](http://wordpress.com/) </div>

## Foursquare

Foursquare is a fun little startup that helps you meet up with friends, discover new places, and save money. Backbone Models are heavily used in the core JavaScript API layer and Views power many popular features like the [homepage map](https://foursquare.com) and [lists](https://foursquare.com/seriouseats/list/the-best-doughnuts-in-ny).

<div style="text-align: center;">[](http://foursquare.com) </div>

## Bitbucket

[Bitbucket](http://www.bitbucket.org) is a free source code hosting service for Git and Mercurial. Through its models and collections, Backbone.js has proved valuable in supporting Bitbucket's [REST API](https://api.bitbucket.org), as well as newer components such as in-line code comments and approvals for pull requests. Mustache templates provide server and client-side rendering, while a custom [Google Closure](https://developers.google.com/closure/library/) inspired life-cycle for widgets allows Bitbucket to decorate existing DOM trees and insert new ones.

<div style="text-align: center;">[](http://www.bitbucket.org) </div>

## Disqus

[Disqus](http://www.disqus.com) chose Backbone.js to power the latest version of their commenting widget. Backbone’s small footprint and easy extensibility made it the right choice for Disqus’ distributed web application, which is hosted entirely inside an iframe and served on thousands of large web properties, including IGN, Wired, CNN, MLB, and more.

<div style="text-align: center;">[](http://www.disqus.com) </div>

## Delicious

[Delicious](https://delicious.com/) is a social bookmarking platform making it easy to save, sort, and store bookmarks from across the web. Delicious uses [Chaplin.js](http://chaplinjs.org), Backbone.js and AppCache to build a full-featured MVC web app. The use of Backbone helped the website and [mobile apps](http://delicious.com/tools) share a single API service, and the reuse of the model tier made it significantly easier to share code during the recent Delicious redesign.

<div style="text-align: center;">[](http://www.delicious.com) </div>

## Khan Academy

[Khan Academy](http://www.khanacademy.org) is on a mission to provide a free world-class education to anyone anywhere. With thousands of videos, hundreds of JavaScript-driven exercises, and big plans for the future, Khan Academy uses Backbone to keep frontend code modular and organized. User profiles and goal setting are implemented with Backbone, [jQuery](http://jquery.com/) and [Handlebars](http://handlebarsjs.com/), and most new feature work is being pushed to the client side, greatly increasing the quality of [the API](https://github.com/Khan/khan-api/).

<div style="text-align: center;">[](http://www.khanacademy.org) </div>

## IRCCloud

[IRCCloud](http://irccloud.com/) is an always-connected IRC client that you use in your browser — often leaving it open all day in a tab. The sleek web interface communicates with an Erlang backend via websockets and the [IRCCloud API](https://github.com/irccloud/irccloud-tools/wiki/API-Overview). It makes heavy use of Backbone.js events, models, views and routing to keep your IRC conversations flowing in real time.

<div style="text-align: center;">[](http://irccloud.com/) </div>

## Pitchfork

[Pitchfork](http://pitchfork.com/) uses Backbone.js to power its site-wide audio player, [Pitchfork.tv](http://pitchfork.com/tv/), location routing, a write-thru page fragment cache, and more. Backbone.js (and [Underscore.js](http://underscorejs.org/)) helps the team create clean and modular components, move very quickly, and focus on the site, not the spaghetti.

<div style="text-align: center;">[](http://pitchfork.com/) </div>

## Spin

[Spin](http://spin.com/) pulls in the [latest news stories](http://www.spin.com/news) from their internal API onto their site using Backbone models and collections, and a custom <tt>sync</tt> method. Because the music should never stop playing, even as you click through to different "pages", Spin uses a Backbone router for navigation within the site.

<div style="text-align: center;">[](http://spin.com/) </div>

## ZocDoc

[ZocDoc](http://www.zocdoc.com) helps patients find local, in-network doctors and dentists, see their real-time availability, and instantly book appointments. On the public side, the webapp uses Backbone.js to handle client-side state and rendering in [search pages](http://www.zocdoc.com/primary-care-doctors/los-angeles-13122pm) and [doctor profiles](http://www.zocdoc.com/doctor/nathan-hashimoto-md-58078). In addition, the new version of the doctor-facing part of the website is a large single-page application that benefits from Backbone's structure and modularity. ZocDoc's Backbone classes are tested with [Jasmine](http://pivotal.github.io/jasmine/), and delivered to the end user with [Cassette](http://getcassette.net/).

<div style="text-align: center;">[](http://www.zocdoc.com) </div>

## Walmart Mobile

[Walmart](http://www.walmart.com/) used Backbone.js to create the new version of [their mobile web application](http://mobile.walmart.com/) and created two new frameworks in the process. [Thorax](http://walmartlabs.github.com/thorax/) provides mixins, inheritable events, as well as model and collection view bindings that integrate directly with [Handlebars](http://handlebarsjs.com/) templates. [Lumbar](http://walmartlabs.github.com/lumbar/) allows the application to be split into modules which can be loaded on demand, and creates platform specific builds for the portions of the web application that are embedded in Walmart's native Android and iOS applications.

<div style="text-align: center;">[](http://mobile.walmart.com/r/phoenix) </div>

## Groupon Now!

[Groupon Now!](http://www.groupon.com/now) helps you find local deals that you can buy and use right now. When first developing the product, the team decided it would be AJAX heavy with smooth transitions between sections instead of full refreshes, but still needed to be fully linkable and shareable. Despite never having used Backbone before, the learning curve was incredibly quick — a prototype was hacked out in an afternoon, and the team was able to ship the product in two weeks. Because the source is minimal and understandable, it was easy to add several Backbone extensions for Groupon Now!: changing the router to handle URLs with querystring parameters, and adding a simple in-memory store for caching repeated requests for the same data.

<div style="text-align: center;">[](http://www.groupon.com/now) </div>

## Basecamp

[37Signals](http://37signals.com/) chose Backbone.js to create the [calendar feature](http://basecamp.com/calendar) of its popular project management software [Basecamp](http://basecamp.com/). The Basecamp Calendar uses Backbone.js models and views in conjunction with the [Eco](https://github.com/sstephenson/eco) templating system to present a polished, highly interactive group scheduling interface.

<div style="text-align: center;">[](http://basecamp.com/calendar) </div>

## Slavery Footprint

[Slavery Footprint](http://slaveryfootprint.org/survey) allows consumers to visualize how their consumption habits are connected to modern-day slavery and provides them with an opportunity to have a deeper conversation with the companies that manufacture the goods they purchased. Based in Oakland, California, the Slavery Footprint team works to engage individuals, groups, and businesses to build awareness for and create deployable action against forced labor, human trafficking, and modern-day slavery through online tools, as well as off-line community education and mobilization programs.

<div style="text-align: center;">[](http://slaveryfootprint.org/survey) </div>

## Stripe

[Stripe](https://stripe.com) provides an API for accepting credit cards on the web. Stripe's [management interface](https://manage.stripe.com) was recently rewritten from scratch in CoffeeScript using Backbone.js as the primary framework, [Eco](https://github.com/sstephenson/eco) for templates, [Sass](http://sass-lang.com/) for stylesheets, and [Stitch](https://github.com/sstephenson/stitch) to package everything together as [CommonJS](http://commonjs.org/) modules. The new app uses [Stripe's API](https://stripe.com/docs/api) directly for the majority of its actions; Backbone.js models made it simple to map client-side models to their corresponding RESTful resources.

<div style="text-align: center;">[](https://stripe.com) </div>

## Airbnb

[Airbnb](http://airbnb.com) uses Backbone in many of its products. It started with [Airbnb Mobile Web](http://m.airbnb.com) (built in six weeks by a team of three) and has since grown to [Wish Lists](https://www.airbnb.com/wishlists/popular), [Match](http://www.airbnb.com/match), [Search](http://www.airbnb.com/s/), Communities, Payments, and Internal Tools.

<div style="text-align: center;">[](http://m.airbnb.com/) </div>

## SoundCloud Mobile

[SoundCloud](http://soundcloud.com) is the leading sound sharing platform on the internet, and Backbone.js provides the foundation for [SoundCloud Mobile](http://m.soundcloud.com). The project uses the public SoundCloud [API](http://soundcloud.com/developers) as a data source (channeled through a nginx proxy), [jQuery templates](http://api.jquery.com/category/plugins/templates/) for the rendering, [Qunit](http://docs.jquery.com/Qunit) and [PhantomJS](http://www.phantomjs.org/) for the testing suite. The JS code, templates and CSS are built for the production deployment with various Node.js tools like [ready.js](https://github.com/dsimard/ready.js), [Jake](https://github.com/mde/jake), [jsdom](https://github.com/tmpvar/jsdom). The **Backbone.History** was modified to support the HTML5 <tt>history.pushState</tt>. **Backbone.sync** was extended with an additional SessionStorage based cache layer.

<div style="text-align: center;">[](http://m.soundcloud.com) </div>

## Art.sy

[Art.sy](http://artsy.net) is a place to discover art you'll love. Art.sy is built on Rails, using [Grape](https://github.com/intridea/grape) to serve a robust [JSON API](http://artsy.net/api). The main site is a single page app written in CoffeeScript and uses Backbone to provide structure around this API. An admin panel and partner CMS have also been extracted into their own API-consuming Backbone projects.

<div style="text-align: center;">[](http://artsy.net) </div>

## Pandora

When [Pandora](http://www.pandora.com/newpandora) redesigned their site in HTML5, they chose Backbone.js to help manage the user interface and interactions. For example, there's a model that represents the "currently playing track", and multiple views that automatically update when the current track changes. The station list is a collection, so that when stations are added or changed, the UI stays up to date.

<div style="text-align: center;">[](http://www.pandora.com/newpandora) </div>

## Inkling

[Inkling](http://inkling.com/) is a cross-platform way to publish interactive learning content. [Inkling for Web](https://www.inkling.com/read/) uses Backbone.js to make hundreds of complex books — from student textbooks to travel guides and programming manuals — engaging and accessible on the web. Inkling supports WebGL-enabled 3D graphics, interactive assessments, social sharing, and a system for running practice code right in the book, all within a single page Backbone-driven app. Early on, the team decided to keep the site lightweight by using only Backbone.js and raw JavaScript. The result? Complete source code weighing in at a mere 350kb with feature-parity across the iPad, iPhone and web clients. Give it a try with [this excerpt from JavaScript: The Definitive Guide](https://www.inkling.com/read/javascript-definitive-guide-david-flanagan-6th/chapter-4/function-definition-expressions).

<div style="text-align: center;">[](http://inkling.com) </div>

## Code School

[Code School](http://www.codeschool.com) courses teach people about various programming topics like [CoffeeScript](http://coffeescript.org), CSS, Ruby on Rails, and more. The new Code School course [challenge page](http://coffeescript.codeschool.com/levels/1/challenges/1) is built from the ground up on Backbone.js, using everything it has to offer: the router, collections, models, and complex event handling. Before, the page was a mess of [jQuery](http://jquery.com/) DOM manipulation and manual Ajax calls. Backbone.js helped introduce a new way to think about developing an organized front-end application in JavaScript.

<div style="text-align: center;">[](http://www.codeschool.com) </div>

## CloudApp

[CloudApp](http://getcloudapp.com) is simple file and link sharing for the Mac. Backbone.js powers the web tools which consume the [documented API](http://developer.getcloudapp.com) to manage Drops. Data is either pulled manually or pushed by [Pusher](http://pusher.com) and fed to [Mustache](http://github.com/janl/mustache.js) templates for rendering. Check out the [annotated source code](http://cloudapp.github.com/engine) to see the magic.

<div style="text-align: center;">[](http://getcloudapp.com) </div>

## SeatGeek

[SeatGeek](http://seatgeek.com)'s stadium ticket maps were originally developed with [Prototype.js](http://prototypejs.org/). Moving to Backbone.js and [jQuery](http://jquery.com/) helped organize a lot of the UI code, and the increased structure has made adding features a lot easier. SeatGeek is also in the process of building a mobile interface that will be Backbone.js from top to bottom.

<div style="text-align: center;">[](http://seatgeek.com) </div>

## Easel

[Easel](http://easel.io) is an in-browser, high fidelity web design tool that integrates with your design and development process. The Easel team uses CoffeeScript, Underscore.js and Backbone.js for their [rich visual editor](http://easel.io/demo) as well as other management functions throughout the site. The structure of Backbone allowed the team to break the complex problem of building a visual editor into manageable components and still move quickly.

<div style="text-align: center;">[](http://easel.io) </div>

## Jolicloud

[Jolicloud](http://www.jolicloud.com/) is an open and independent platform and [operating system](http://www.jolicloud.com/jolios) that provides music playback, video streaming, photo browsing and document editing — transforming low cost computers into beautiful cloud devices. The [new Jolicloud HTML5 app](https://my.jolicloud.com/) was built from the ground up using Backbone and talks to the [Jolicloud Platform](http://developers.jolicloud.com), which is based on Node.js. Jolicloud works offline using the HTML5 AppCache, extends Backbone.sync to store data in IndexedDB or localStorage, and communicates with the [Joli OS](http://www.jolicloud.com/jolios) via WebSockets.

<div style="text-align: center;">[](http://jolicloud.com/) </div>

## Salon.io

[Salon.io](http://salon.io) provides a space where photographers, artists and designers freely arrange their visual art on virtual walls. [Salon.io](http://salon.io) runs on [Rails](http://rubyonrails.org/), but does not use much of the traditional stack, as the entire frontend is designed as a single page web app, using Backbone.js, [Brunch](http://brunch.io/) and [CoffeeScript](http://coffeescript.org).

<div style="text-align: center;">[](http://salon.io) </div>

## TileMill

Our fellow [Knight Foundation News Challenge](http://www.newschallenge.org/) winners, [MapBox](http://mapbox.com/), created an open-source map design studio with Backbone.js: [TileMill](https://www.mapbox.com/tilemill/). TileMill lets you manage map layers based on shapefiles and rasters, and edit their appearance directly in the browser with the [Carto styling language](https://github.com/mapbox/carto). Note that the gorgeous [MapBox](http://mapbox.com/) homepage is also a Backbone.js app.

<div style="text-align: center;">[](https://www.mapbox.com/tilemill/) </div>

## Blossom

[Blossom](http://blossom.io) is a lightweight project management tool for lean teams. Backbone.js is heavily used in combination with [CoffeeScript](http://coffeescript.org) to provide a smooth interaction experience. The app is packaged with [Brunch](http://brunch.io). The RESTful backend is built with [Flask](http://flask.pocoo.org/) on Google App Engine.

<div style="text-align: center;">[](http://blossom.io) </div>

## Trello

[Trello](http://trello.com) is a collaboration tool that organizes your projects into boards. A Trello board holds many lists of cards, which can contain checklists, files and conversations, and may be voted on and organized with labels. Updates on the board happen in real time. The site was built ground up using Backbone.js for all the models, views, and routes.

<div style="text-align: center;">[](http://trello.com) </div>

## Tzigla

[Cristi Balan](http://twitter.com/evilchelu) and [Irina Dumitrascu](http://dira.ro) created [Tzigla](http://tzigla.com), a collaborative drawing application where artists make tiles that connect to each other to create [surreal drawings](http://tzigla.com/boards/1). Backbone models help organize the code, routers provide [bookmarkable deep links](http://tzigla.com/boards/1#!/tiles/2-2), and the views are rendered with [haml.js](https://github.com/creationix/haml-js) and [Zepto](http://zeptojs.com/). Tzigla is written in Ruby ([Rails](http://rubyonrails.org/)) on the backend, and [CoffeeScript](http://coffeescript.org) on the frontend, with [Jammit](http://documentcloud.github.com/jammit/) prepackaging the static assets.

<div style="text-align: center;">[](http://www.tzigla.com/) </div>

## Change Log

**1.3.3** — <small>_Apr. 5, 2016_</small> — [Diff](https://github.com/jashkenas/backbone/compare/1.2.3...1.3.3) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/1.3.3/index.html)  

*   Added <tt>findIndex</tt> and <tt>findLastIndex</tt> Underscore methods to <tt>Collection</tt>.
*   Added <tt>options.changes</tt> to <tt>Collection</tt> "update" event which includes added, merged, and removed models.
*   Ensured <tt>Collection#reduce</tt> and <tt>Collection#reduceRight</tt> work without an initial <tt>accumulator</tt> value.
*   Ensured <tt>Collection#_removeModels</tt> always returns an array.
*   Fixed a bug where <tt>Events.once</tt> with object syntax failed to bind context.
*   Fixed <tt>Collection#_onModelEvent</tt> regression where triggering a <tt>change</tt> event without a <tt>model</tt> would error.
*   Fixed <tt>Collection#set</tt> regression when <tt>parse</tt> returns a falsy value.
*   Fixed <tt>Model#id</tt> regression where <tt>id</tt> would be unintentionally <tt>undefined</tt>.
*   Fixed <tt>_removeModels</tt> regression which could cause an infinite loop under certain conditions.
*   Removed <tt>component</tt> package support.
*   Note that 1.3.3 fixes several bugs in versions 1.3.0 to 1.3.2\. Please upgrade immediately if you are on one of those versions.

**1.2.3** — <small>_Sept. 3, 2015_</small> — [Diff](https://github.com/jashkenas/backbone/compare/1.2.2...1.2.3) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/1.2.3/index.html)  

*   Fixed a minor regression in 1.2.2 that would cause an error when adding a model to a collection <tt>at</tt> an out of bounds index.

**1.2.2** — <small>_Aug. 19, 2015_</small> — [Diff](https://github.com/jashkenas/backbone/compare/1.2.1...1.2.2) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/1.2.2/index.html)  

*   Collection methods <tt>find</tt>, <tt>filter</tt>, <tt>reject</tt>, <tt>every</tt>, <tt>some</tt>, and <tt>partition</tt> can now take a model-attributes-style predicate: <tt>this.collection.reject({user: 'guybrush'})</tt>.
*   Backbone Events once again supports multiple-event maps (<tt>obj.on({'error change': action})</tt>). This was a previously undocumented feature inadvertently removed in 1.2.0.
*   Added <tt>Collection#includes</tt> as an alias of <tt>Collection#contains</tt> and as a replacement for <tt>Collection#include</tt> in Underscore.js >= 1.8.

**1.2.1** — <small>_Jun. 4, 2015_</small> — [Diff](https://github.com/jashkenas/backbone/compare/1.2.0...1.2.1) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/1.2.1/index.html)  

*   <tt>Collection#add</tt> now avoids trying to parse a model instance when passed <tt>parse: false</tt>.
*   Bug fix in <tt>Collection#remove</tt>. The removed models are now actually returned.
*   <tt>Model#fetch</tt> no longer parses the response when passing <tt>parse: false</tt>.
*   Bug fix for iframe-based History when used with JSDOM.
*   Bug fix where <tt>Collection#invoke</tt> was not taking additional arguments.
*   When using <tt>on</tt> with an event map, you can now pass the context as the second argument. This was a previously undocumented feature inadvertently removed in 1.2.0.

**1.2.0** — <small>_May 13, 2015_</small> — [Diff](https://github.com/jashkenas/backbone/compare/1.1.2...1.2.0) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/1.2.0/index.html)  

*   Added new hooks to Views to allow them to work without jQuery. See the [wiki page](https://github.com/jashkenas/backbone/wiki/Using-Backbone-without-jQuery) for more info.
*   As a neat side effect, Backbone.History no longer uses jQuery's event methods for <tt>pushState</tt> and <tt>hashChange</tt> listeners. We're native all the way.
*   Also on the subject of jQuery, if you're using Backbone with CommonJS (node, browserify, webpack) Backbone will automatically try to load jQuery for you.
*   Views now always delegate their events in [setElement](#View-setElement). You can no longer modify the events hash or your view's <tt>el</tt> property in <tt>initialize</tt>.
*   Added an <tt>"update"</tt> event that triggers after any amount of models are added or removed from a collection. Handy to re-render lists of things without debouncing.
*   <tt>Collection#at</tt> can take a negative index.
*   Added <tt>modelId</tt> to Collection for generating unique ids on polymorphic collections. Handy for cases when your model ids would otherwise collide.
*   Added an overridable <tt>_isModel</tt> for more advanced control of what's considered a model by your Collection.
*   The <tt>success</tt> callback passed to <tt>Model#destroy</tt> is always called asynchronously now.
*   <tt>Router#execute</tt> passes back the route name as its third argument.
*   Cancel the current Router transition by returning <tt>false</tt> in <tt>Router#execute</tt>. Great for checking logged-in status or other prerequisites.
*   Added <tt>getSearch</tt> and <tt>getPath</tt> methods to Backbone.History as cross-browser and overridable ways of slicing up the URL.
*   Added <tt>delegate</tt> and <tt>undelegate</tt> as finer-grained versions of <tt>delegateEvents</tt> and <tt>undelegateEvents</tt>. Useful for plugin authors to use a consistent events interface in Backbone.
*   A collection will only fire a "sort" event if its order was actually updated, not on every <tt>set</tt>.
*   Any passed <tt>options.attrs</tt> are now respected when saving a model with <tt>patch: true</tt>.
*   <tt>Collection#clone</tt> now sets the <tt>model</tt> and <tt>comparator</tt> functions of the cloned collection to the new one.
*   Adding models to your Collection when specifying an <tt>at</tt> position now sends the actual position of your model in the <tt>add</tt> event, not just the one you've passed in.
*   <tt>Collection#remove</tt> will now only return a list of models that have actually been removed from the collection.
*   Fixed loading Backbone.js in strict ES6 module loaders.

**1.1.2** — <small>_Feb. 20, 2014_</small> — [Diff](https://github.com/jashkenas/backbone/compare/1.1.1...1.1.2) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/1.1.2/index.html)  

*   Backbone no longer tries to require jQuery in Node/CommonJS environments, for better compatibility with folks using Browserify. If you'd like to have Backbone use jQuery from Node, assign it like so: <tt>Backbone.$ = require('jquery');</tt>
*   Bugfix for route parameters with newlines in them.

**1.1.1** — <small>_Feb. 13, 2014_</small> — [Diff](https://github.com/jashkenas/backbone/compare/1.1.0...1.1.1) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/1.1.1/index.html)  

*   Backbone now registers itself for AMD (Require.js), Bower and Component, as well as being a CommonJS module and a regular (Java)Script. Whew.
*   Added an <tt>execute</tt> hook to the Router, which allows you to hook in and custom-parse route arguments, like query strings, for example.
*   Performance fine-tuning for Backbone Events.
*   Better matching for Unicode in routes, in old browsers.
*   Backbone Routers now handle query params in route fragments, passing them into the handler as the last argument. Routes specified as strings should no longer include the query string (<tt>'foo?:query'</tt> should be <tt>'foo'</tt>).

**1.1.0** — <small>_Oct. 10, 2013_</small> — [Diff](https://github.com/jashkenas/backbone/compare/1.0.0...1.1.0) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/1.1.0/index.html)  

*   Made the return values of Collection's <tt>set</tt>, <tt>add</tt>, <tt>remove</tt>, and <tt>reset</tt> more useful. Instead of returning <tt>this</tt>, they now return the changed (added, removed or updated) model or list of models.
*   Backbone Views no longer automatically attach options passed to the constructor as <tt>this.options</tt> and Backbone Models no longer attach <tt>url</tt> and <tt>urlRoot</tt> options, but you can do it yourself if you prefer.
*   All <tt>"invalid"</tt> events now pass consistent arguments. First the model in question, then the error object, then options.
*   You are no longer permitted to change the **id** of your model during <tt>parse</tt>. Use <tt>idAttribute</tt> instead.
*   On the other hand, <tt>parse</tt> is now an excellent place to extract and vivify incoming nested JSON into associated submodels.
*   Many tweaks, optimizations and bugfixes relating to Backbone 1.0, including URL overrides, mutation of options, bulk ordering, trailing slashes, edge-case listener leaks, nested model parsing...

**1.0.0** — <small>_March 20, 2013_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.9.10...1.0.0) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/1.0.0/index.html)  

*   Renamed Collection's "update" to [set](#Collection-set), for parallelism with the similar <tt>model.set()</tt>, and contrast with [reset](#Collection-reset). It's now the default updating mechanism after a [fetch](#Collection-fetch). If you'd like to continue using "reset", pass <tt>{reset: true}</tt>.
*   Your route handlers will now receive their URL parameters pre-decoded.
*   Added [listenToOnce](#Events-listenToOnce) as the analogue of [once](#Events-once).
*   Added the [findWhere](#Collection-findWhere) method to Collections, similar to [where](#Collection-where).
*   Added the <tt>keys</tt>, <tt>values</tt>, <tt>pairs</tt>, <tt>invert</tt>, <tt>pick</tt>, and <tt>omit</tt> Underscore.js methods to Backbone Models.
*   The routes in a Router's route map may now be function literals, instead of references to methods, if you like.
*   <tt>url</tt> and <tt>urlRoot</tt> properties may now be passed as options when instantiating a new Model.

**0.9.10** — <small>_Jan. 15, 2013_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.9.9...0.9.10) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.9.10/index.html)  

*   A <tt>"route"</tt> event is triggered on the router in addition to being fired on <tt>Backbone.history</tt>.
*   Model validation is now only enforced by default in <tt>Model#save</tt> and no longer enforced by default upon construction or in <tt>Model#set</tt>, unless the <tt>{validate:true}</tt> option is passed.
*   <tt>View#make</tt> has been removed. You'll need to use <tt>$</tt> directly to construct DOM elements now.
*   Passing <tt>{silent:true}</tt> on change will no longer delay individual <tt>"change:attr"</tt> events, instead they are silenced entirely.
*   The <tt>Model#change</tt> method has been removed, as delayed attribute changes are no longer available.
*   Bug fix on <tt>change</tt> where attribute comparison uses <tt>!==</tt> instead of <tt>_.isEqual</tt>.
*   Bug fix where an empty response from the server on save would not call the success function.
*   <tt>parse</tt> now receives <tt>options</tt> as its second argument.
*   Model validation now fires <tt>invalid</tt> event instead of <tt>error</tt>.

**0.9.9** — <small>_Dec. 13, 2012_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.9.2...0.9.9) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.9.9/index.html)  

*   Added [listenTo](#Events-listenTo) and [stopListening](#Events-stopListening) to Events. They can be used as inversion-of-control flavors of <tt>on</tt> and <tt>off</tt>, for convenient unbinding of all events an object is currently listening to. <tt>view.remove()</tt> automatically calls <tt>view.stopListening()</tt>.
*   When using <tt>add</tt> on a collection, passing <tt>{merge: true}</tt> will now cause duplicate models to have their attributes merged in to the existing models, instead of being ignored.
*   Added [update](#Collection-update) (which is also available as an option to <tt>fetch</tt>) for "smart" updating of sets of models.
*   HTTP <tt>PATCH</tt> support in [save](#Model-save) by passing <tt>{patch: true}</tt>.
*   The <tt>Backbone</tt> object now extends <tt>Events</tt> so that you can use it as a global event bus, if you like.
*   Added a <tt>"request"</tt> event to [Backbone.sync](#Sync), which triggers whenever a request begins to be made to the server. The natural complement to the <tt>"sync"</tt> event.
*   Router URLs now support optional parts via parentheses, without having to use a regex.
*   Backbone events now supports <tt>once</tt>, similar to Node's <tt>once</tt>, or jQuery's <tt>one</tt>.
*   Backbone events now support jQuery-style event maps <tt>obj.on({click: action})</tt>.
*   While listening to a <tt>reset</tt> event, the list of previous models is now available in <tt>options.previousModels</tt>, for convenience.
*   [Validation](#Model-validate) now occurs even during "silent" changes. This change means that the <tt>isValid</tt> method has been removed. Failed validations also trigger an error, even if an error callback is specified in the options.
*   Consolidated <tt>"sync"</tt> and <tt>"error"</tt> events within [Backbone.sync](#Sync). They are now triggered regardless of the existence of <tt>success</tt> or <tt>error</tt> callbacks.
*   For mixed-mode APIs, <tt>Backbone.sync</tt> now accepts <tt>emulateHTTP</tt> and <tt>emulateJSON</tt> as inline options.
*   Collections now also proxy Underscore method name aliases (collect, inject, foldl, foldr, head, tail, take, and so on...)
*   Removed <tt>getByCid</tt> from Collections. <tt>collection.get</tt> now supports lookup by both <tt>id</tt> and <tt>cid</tt>.
*   After fetching a model or a collection, _all_ defined <tt>parse</tt> functions will now be run. So fetching a collection and getting back new models could cause both the collection to parse the list, and then each model to be parsed in turn, if you have both functions defined.
*   Bugfix for normalizing leading and trailing slashes in the Router definitions. Their presence (or absence) should not affect behavior.
*   When declaring a View, <tt>options</tt>, <tt>el</tt>, <tt>tagName</tt>, <tt>id</tt> and <tt>className</tt> may now be defined as functions, if you want their values to be determined at runtime.
*   Added a <tt>Backbone.ajax</tt> hook for more convenient overriding of the default use of <tt>$.ajax</tt>. If AJAX is too passé, set it to your preferred method for server communication.
*   <tt>Collection#sort</tt> now triggers a <tt>sort</tt> event, instead of a <tt>reset</tt> event.
*   Calling <tt>destroy</tt> on a Model will now return <tt>false</tt> if the model <tt>isNew</tt>.
*   To set what library Backbone uses for DOM manipulation and Ajax calls, use <tt>Backbone.$ = ...</tt> instead of <tt>setDomLibrary</tt>.
*   Removed the <tt>Backbone.wrapError</tt> helper method. Overriding <tt>sync</tt> should work better for those particular use cases.
*   To improve the performance of <tt>add</tt>, <tt>options.index</tt> will no longer be set in the <tt>add</tt> event callback. <tt>collection.indexOf(model)</tt> can be used to retrieve the index of a model as necessary.
*   For semantic and cross browser reasons, routes will now ignore search parameters. Routes like <tt>search?query=…&page=3</tt> should become <tt>search/…/3</tt>.
*   <tt>Model#set</tt> no longer accepts another model as an argument. This leads to subtle problems and is easily replaced with <tt>model.set(other.attributes)</tt>.

**0.9.2** — <small>_March 21, 2012_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.9.1...0.9.2) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.9.2/index.html)  

*   Instead of throwing an error when adding duplicate models to a collection, Backbone will now silently skip them instead.
*   Added [push](#Collection-push), [pop](#Collection-pop), [unshift](#Collection-unshift), and [shift](#Collection-shift) to collections.
*   A model's [changed](#Model-changed) hash is now exposed for easy reading of the changed attribute delta, since the model's last <tt>"change"</tt> event.
*   Added [where](#Collection-where) to collections for simple filtering.
*   You can now use a single [off](#Events-off) call to remove all callbacks bound to a specific object.
*   Bug fixes for nested individual change events, some of which may be "silent".
*   Bug fixes for URL encoding in <tt>location.hash</tt> fragments.
*   Bug fix for client-side validation in advance of a <tt>save</tt> call with <tt>{wait: true}</tt>.
*   Updated / refreshed the example [Todo List](examples/todos/index.html) app.

**0.9.1** — <small>_Feb. 2, 2012_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.9.0...0.9.1) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.9.1/index.html)  

*   Reverted to 0.5.3-esque behavior for validating models. Silent changes no longer trigger validation (making it easier to work with forms). Added an <tt>isValid</tt> function that you can use to check if a model is currently in a valid state.
*   If you have multiple versions of jQuery on the page, you can now tell Backbone which one to use with <tt>Backbone.setDomLibrary</tt>.
*   Fixes regressions in **0.9.0** for routing with "root", saving with both "wait" and "validate", and the order of nested "change" events.

**0.9.0** — <small>_Jan. 30, 2012_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.5.3...0.9.0) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.9.0/index.html)  

*   Creating and destroying models with <tt>create</tt> and <tt>destroy</tt> are now optimistic by default. Pass <tt>{wait: true}</tt> as an option if you'd like them to wait for a successful server response to proceed.
*   Two new properties on views: <tt>$el</tt> — a cached jQuery (or Zepto) reference to the view's element, and <tt>setElement</tt>, which should be used instead of manually setting a view's <tt>el</tt>. It will both set <tt>view.el</tt> and <tt>view.$el</tt> correctly, as well as re-delegating events on the new DOM element.
*   You can now bind and trigger multiple spaced-delimited events at once. For example: <tt>model.on("change:name change:age", ...)</tt>
*   When you don't know the key in advance, you may now call <tt>model.set(key, value)</tt> as well as <tt>save</tt>.
*   Multiple models with the same <tt>id</tt> are no longer allowed in a single collection.
*   Added a <tt>"sync"</tt> event, which triggers whenever a model's state has been successfully synced with the server (create, save, destroy).
*   <tt>bind</tt> and <tt>unbind</tt> have been renamed to <tt>on</tt> and <tt>off</tt> for clarity, following jQuery's lead. The old names are also still supported.
*   A Backbone collection's <tt>comparator</tt> function may now behave either like a [sortBy](http://underscorejs.org/#sortBy) (pass a function that takes a single argument), or like a [sort](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) (pass a comparator function that expects two arguments). The comparator function is also now bound by default to the collection — so you can refer to <tt>this</tt> within it.
*   A view's <tt>events</tt> hash may now also contain direct function values as well as the string names of existing view methods.
*   Validation has gotten an overhaul — a model's <tt>validate</tt> function will now be run even for silent changes, and you can no longer create a model in an initially invalid state.
*   Added <tt>shuffle</tt> and <tt>initial</tt> to collections, proxied from Underscore.
*   <tt>Model#urlRoot</tt> may now be defined as a function as well as a value.
*   <tt>View#attributes</tt> may now be defined as a function as well as a value.
*   Calling <tt>fetch</tt> on a collection will now cause all fetched JSON to be run through the collection's model's <tt>parse</tt> function, if one is defined.
*   You may now tell a router to <tt>navigate(fragment, {replace: true})</tt>, which will either use <tt>history.replaceState</tt> or <tt>location.hash.replace</tt>, in order to change the URL without adding a history entry.
*   Within a collection's <tt>add</tt> and <tt>remove</tt> events, the index of the model being added or removed is now available as <tt>options.index</tt>.
*   Added an <tt>undelegateEvents</tt> to views, allowing you to manually remove all configured event delegations.
*   Although you shouldn't be writing your routes with them in any case — leading slashes (<tt>/</tt>) are now stripped from routes.
*   Calling <tt>clone</tt> on a model now only passes the attributes for duplication, not a reference to the model itself.
*   Calling <tt>clear</tt> on a model now removes the <tt>id</tt> attribute.

**0.5.3** — <small>_August 9, 2011_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.5.2...0.5.3) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.5.3/index.html)  
A View's <tt>events</tt> property may now be defined as a function, as well as an object literal, making it easier to programmatically define and inherit events. <tt>groupBy</tt> is now proxied from Underscore as a method on Collections. If the server has already rendered everything on page load, pass <tt>Backbone.history.start({silent: true})</tt> to prevent the initial route from triggering. Bugfix for pushState with encoded URLs.

**0.5.2** — <small>_July 26, 2011_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.5.1...0.5.2) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.5.2/index.html)  
The <tt>bind</tt> function, can now take an optional third argument, to specify the <tt>this</tt> of the callback function. Multiple models with the same <tt>id</tt> are now allowed in a collection. Fixed a bug where calling <tt>.fetch(jQueryOptions)</tt> could cause an incorrect URL to be serialized. Fixed a brief extra route fire before redirect, when degrading from <tt>pushState</tt>.

**0.5.1** — <small>_July 5, 2011_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.5.0...0.5.1) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.5.1/index.html)  
Cleanups from the 0.5.0 release, to wit: improved transparent upgrades from hash-based URLs to pushState, and vice-versa. Fixed inconsistency with non-modified attributes being passed to <tt>Model#initialize</tt>. Reverted a **0.5.0** change that would strip leading hashbangs from routes. Added <tt>contains</tt> as an alias for <tt>includes</tt>.

**0.5.0** — <small>_July 1, 2011_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.3.3...0.5.0) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.5.0/index.html)  
A large number of tiny tweaks and micro bugfixes, best viewed by looking at [the commit diff](https://github.com/jashkenas/backbone/compare/0.3.3...0.5.0). HTML5 <tt>pushState</tt> support, enabled by opting-in with: <tt>Backbone.history.start({pushState: true})</tt>. <tt>Controller</tt> was renamed to <tt>Router</tt>, for clarity. <tt>Collection#refresh</tt> was renamed to <tt>Collection#reset</tt> to emphasize its ability to both reset the collection with new models, as well as empty out the collection when used with no parameters. <tt>saveLocation</tt> was replaced with <tt>navigate</tt>. RESTful persistence methods (save, fetch, etc.) now return the jQuery deferred object for further success/error chaining and general convenience. Improved XSS escaping for <tt>Model#escape</tt>. Added a <tt>urlRoot</tt> option to allow specifying RESTful urls without the use of a collection. An error is thrown if <tt>Backbone.history.start</tt> is called multiple times. <tt>Collection#create</tt> now validates before initializing the new model. <tt>view.el</tt> can now be a jQuery string lookup. Backbone Views can now also take an <tt>attributes</tt> parameter. <tt>Model#defaults</tt> can now be a function as well as a literal attributes object.

**0.3.3** — <small>_Dec 1, 2010_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.3.2...0.3.3) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.3.3/index.html)  
Backbone.js now supports [Zepto](http://zeptojs.com), alongside jQuery, as a framework for DOM manipulation and Ajax support. Implemented [Model#escape](#Model-escape), to efficiently handle attributes intended for HTML interpolation. When trying to persist a model, failed requests will now trigger an <tt>"error"</tt> event. The ubiquitous <tt>options</tt> argument is now passed as the final argument to all <tt>"change"</tt> events.

**0.3.2** — <small>_Nov 23, 2010_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.3.1...0.3.2) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.3.2/index.html)  
Bugfix for IE7 + iframe-based "hashchange" events. <tt>sync</tt> may now be overridden on a per-model, or per-collection basis. Fixed recursion error when calling <tt>save</tt> with no changed attributes, within a <tt>"change"</tt> event.

**0.3.1** — <small>_Nov 15, 2010_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.3.0...0.3.1) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.3.1/index.html)  
All <tt>"add"</tt> and <tt>"remove"</tt> events are now sent through the model, so that views can listen for them without having to know about the collection. Added a <tt>remove</tt> method to [Backbone.View](#View). <tt>toJSON</tt> is no longer called at all for <tt>'read'</tt> and <tt>'delete'</tt> requests. Backbone routes are now able to load empty URL fragments.

**0.3.0** — <small>_Nov 9, 2010_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.2.0...0.3.0) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.3.0/index.html)  
Backbone now has [Controllers](#Controller) and [History](#History), for doing client-side routing based on URL fragments. Added <tt>emulateHTTP</tt> to provide support for legacy servers that don't do <tt>PUT</tt> and <tt>DELETE</tt>. Added <tt>emulateJSON</tt> for servers that can't accept <tt>application/json</tt> encoded requests. Added [Model#clear](#Model-clear), which removes all attributes from a model. All Backbone classes may now be seamlessly inherited by CoffeeScript classes.

**0.2.0** — <small>_Oct 25, 2010_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.1.2...0.2.0) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.2.0/index.html)  
Instead of requiring server responses to be namespaced under a <tt>model</tt> key, now you can define your own [parse](#Model-parse) method to convert responses into attributes for Models and Collections. The old <tt>handleEvents</tt> function is now named [delegateEvents](#View-delegateEvents), and is automatically called as part of the View's constructor. Added a [toJSON](#Collection-toJSON) function to Collections. Added [Underscore's chain](#Collection-chain) to Collections.

**0.1.2** — <small>_Oct 19, 2010_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.1.1...0.1.2) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.1.2/index.html)  
Added a [Model#fetch](#Model-fetch) method for refreshing the attributes of single model from the server. An <tt>error</tt> callback may now be passed to <tt>set</tt> and <tt>save</tt> as an option, which will be invoked if validation fails, overriding the <tt>"error"</tt> event. You can now tell backbone to use the <tt>_method</tt> hack instead of HTTP methods by setting <tt>Backbone.emulateHTTP = true</tt>. Existing Model and Collection data is no longer sent up unnecessarily with <tt>GET</tt> and <tt>DELETE</tt> requests. Added a <tt>rake lint</tt> task. Backbone is now published as an [NPM](http://npmjs.org) module.

**0.1.1** — <small>_Oct 14, 2010_</small> — [Diff](https://github.com/jashkenas/backbone/compare/0.1.0...0.1.1) — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.1.1/index.html)  
Added a convention for <tt>initialize</tt> functions to be called upon instance construction, if defined. Documentation tweaks.

**0.1.0** — <small>_Oct 13, 2010_</small> — [Docs](https://cdn.rawgit.com/jashkenas/backbone/0.1.0/index.html)  
Initial Backbone release.

[![A DocumentCloud Project](http://jashkenas.s3.amazonaws.com/images/a_documentcloud_project.png)](http://documentcloud.org/ "A DocumentCloud Project") 

</div>

<script>// Set up the "play" buttons for each runnable code example. $(function() { $('.runnable').each(function() { var code = this; var button = '<div class="run" title="Run"></div>'; $(button).insertBefore(code).bind('click', function(){ eval($(code).text()); }); }); $('[data-original]').lazyload(); });</script>