![Backbone.js](docs/images/backbone.png)

Backbone.js为WEB应用程序提供模型(**models**)、集合(**collections**)和视图(**views**)的结构。其中模型可以绑定键值数据和自定义事件；集合带有可枚举函数的丰富API； 视图可以声明事件处理函数，并通过RESRful JSON接口连接到所有您现有的API。

本项目托管在[GitHub](http://github.com/jashkenas/backbone/)， 并且提供[带注释的源代码/a>， 在线](docs/backbone.html)[测试套件](test/)，[应用案例](examples/todos/index.html)，[教程列表](https://github.com/jashkenas/backbone/wiki/Tutorials%2C-blog-posts-and-example-sites) 和[长长的应用Backbone的真实项目列表](#examples)。 Backbone 允许在[MIT 软件协议](http://github.com/jashkenas/backbone/blob/master/LICENSE)下使用。

你可以通过 [GitHub issues page](http://github.com/jashkenas/backbone/issues)和 Freenode IRC 的<tt>#documentcloud</tt> 频道提交bug或参与讨论， 给我们的 [Google Group](https://groups.google.com/forum/#!forum/backbonejs)发送问题， 添加 [wiki](https://github.com/jashkenas/backbone/wiki)页， 或给我们发tweets [@documentcloud](http://twitter.com/documentcloud)。

_Backbone是[DocumentCloud](http://documentcloud.org/)的一个开源组件。_

## 下载 & 依赖 (右键, 选择 "另存为...")

<table>

<tbody>

<tr>

<td>[开发版本(1.3.3)](backbone.js)</td>

<td>_72kb，完整源代码，包含大量注释_</td>

</tr>

<tr>

<td>[产品版本(1.3.3)](backbone-min.js)</td>

<td>_7.6kb， 打包或通过gzip压缩后_  
<small>([Source Map](backbone-min.map))</small></td>

</tr>

<tr>

<td>[修订版本 (master)](https://raw.github.com/jashkenas/backbone/master/backbone.js)</td>

<td>_未发布, 使用风险自负_ [![](https://travis-ci.org/jashkenas/backbone.png)](https://travis-ci.org/jashkenas/backbone) </td>

</tr>

</tbody>

</table>

Backbone 仅仅强依赖于 [Underscore.js](http://underscorejs.org/) <small>( >= 1.8.3)</small>。 [Backbone.View](#View)处理RESTful持久化和DOM维护需要包含： **[jQuery](https://jquery.com/)** ( >= 1.11.0)，和 **[json2.js](https://github.com/douglascrockford/JSON-js)** （为旧浏览器添加json的支持）。 _（类似 Underscore 和 jQuery 的库，比如 [Lodash](https://lodash.com/) 和 [Zepto](http://zeptojs.com/)， 也可以驱动运行，但需要注意它们的浏览器兼容性。）_

## 开始（Getting Started）

当开发需要包含大量javascript的web应用程序时，第一件事便是你要学会停止向DOM添加数据。 通过复杂多变的jQuery选择符和回调函数，以及通过维持HTML UI、Javascript逻辑和服务器数据库之间的同步来创建Javascript应用程序都很容易。 但对于富客户端应用来说，一个更加结构化的方法通常很有帮助。

通过 Backbone, 使用[Models](#Model)来表示您的数据， 模型可以被创建、验证、销毁或保存到服务器。任何时候，当一个UI动作导致模型的属性发生变化，模型会触发一个 _"change"_ 事件，所有显示模型状态的[Views](#View)都会接收到该事件的通知， 因此它们能够做出响应，利用新状态信息重新渲染自己。 在一个已完成的Backbone应用中，你不需要写胶水代码通过一个特殊_id_去查找DOM元素并手动更新HTML — 当模型发生变化，视图自动更新。

从逻辑上讲，Backbone试图去涵盖数据结构（models and collections）和用户界面（views and URLs）单元最小集来帮助您用javascript构建web应用。 在包罗万象的javascript生态系统中，框架（frameworks）通常帮你决定了所有的事情，很多库（libraries）要求你的站点按照它们的方式、风格和行为来组织 — Backbone 将会继续作为一个工具，让你可以_自由_的去设计web应用的完整体验。

如果您是位新手，不确定Backbone能做什么，可以先浏览一下[基于Backbone的项目列表](#examples).

文档中的大多数示例代码都是可以运行的，因为本页已包含了Backbone，点击_play_按钮去执行他们。

## 模型和视图（Models and Views）

![Model-View Separation.](docs/images/intro-model-view.svg)

最大的亮点是Backbone可以帮助你分离业务逻辑和用户界面，当两者纠缠在一起的时候，改变就很困难；当逻辑不依赖UI，用户界面将会容易处理。

<div class="columns">

<div class="col-50">**模型（Model）**

*   协调数据和业务逻辑
*   从服务器加载和保存
*   当数据改变时发出事件通知

</div>

<div class="col-50">**视图（View）**

*   监听变化并渲染UI
*   处理用户输入和交互
*   捕捉输入并发送给模型

</div>

</div>

一个模型（**Model**）管理着一个内部数据属性表，任何数据被修改将会触发一个<tt>"change"</tt> 事件。 模型通过一个持久化层（通常是一个REST API和一个后台数据库）来同步处理数据。 设计你的模型作为原子可重用对象，包含操作特定数据的所有操作功能。 模型应该包含必要的数据，并可以在应用的任何地方使用。

一个视图（**View**）是一个用户界面原子块，它常常用来渲染一个特殊模型或许多模型中的数据， — 但是视图也可以是一个微数据（data-less）的独立UI块。模型通常不关心视图，相反，视图监听模型的<tt>"change"</tt>事件，并做出响应或适当的重新渲染它们自己。

## 集合（Collections）

![Model Collections.](docs/images/intro-collections.svg)

一个集合（**Collection**）帮助你处理一组相关的模型，处理新模型的加载和保存到服务器，提供辅助功能来执行聚合或计算模型列表。 除了自己的事件，集合也代理所有内部模型所发生的事件，让你可以在一个地方监听集合中任何模型可能发生的任何变化。

## API整合（API Integration）

Backbone 是一个预配置的同步 RESTful API，创建一个新的集合，并通过<tt>url</tt>来指定资源点：

<pre>var Books = Backbone.Collection.extend({
  url: '/books'
});
</pre>

集合（**Collection**）和模型（**Model**）组件结合使用下面的方法形成了REST资源的直接映射：

<pre>GET  /books/ .... collection.fetch();
POST /books/ .... collection.create();
GET  /books/1 ... model.fetch();
PUT  /books/1 ... model.save();
DEL  /books/1 ... model.destroy();
</pre>

当通过API抓取到原始的JSON数据后，集合（**Collection**）将数据格式化为数组自动填充自己，模型（**Model**）则将数据格式化成对象自动填充自己：

<pre>[{"id": 1}] ..... populates a Collection with one model.
{"id": 1} ....... populates a Model with one attribute.
</pre>

但是，常常遇到APIs返回的数据非Backbone所期望的格式，例如，考虑从一个API获取一个集合， 但返回的数组数据被包裹在元数据中：

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

上面的示例数据中，集合（**Collection** ）应该使用<tt>"books"</tt>数组填充，而不是根对象。 这种情况很容易解决，通过使用<tt>parse</tt>方法来返回（或转换）所期望的数据部分：

<pre>var Books = Backbone.Collection.extend({
  url: '/books',
  parse: function(data) {
    return data.books;
  }
});
</pre>

## （视图渲染）View Rendering

![View rendering.](docs/images/intro-views.svg)

每个视图（**View**）管理自己DOM元素的渲染和用户交互，如果严格要求视图不允许扩展到自身以外，将有助于保持界面灵活 — 允许视图在需要它们的任何地方单独渲染。

Backbone 保留渲染视图（**View**）对象和它们子视图为UI的处理方式：你可以自定义模型如何编译为HTML（或者SVG、Canvas以及其它更奇异的对象）。 它可以是一个简单的[Underscore 微模板](http://underscorejs.org/#template)，也可以是流行的[React virtual DOM](http://facebook.github.io/react/docs/tutorial.html)。可以在[Backbone primer](https://github.com/jashkenas/backbone/wiki/Backbone%2C-The-Primer)找到一些基础的渲染视图方法。

## （URLs路由） Routing with URLs

![Routing](docs/images/intro-routing.svg)

在富web应用程序中，我仍然想去为应用有意义的位置提供可链接、可添加书签和可分享URLs。 每当用户到达应用程序的一个新“地方”，使用路由（**Router**）去更新浏览器的URL，它们可能需要添加书签或分享。 相反，路由（**Router**）检测 URL 的改变，— 也就是说，点击“返回”按钮，— 可以确切的告诉你的应用程序你现在的位置。

## 事件（Backbone.Events）

事件（**Events**）是一个可以被混合到任何对象的模块，为对象提供绑定和触发自定义命名事件的能力，绑定事件前不需要声明，可以传递参数，例如：

<pre class="runnable">var object = {};

_.extend(object, Backbone.Events);

object.on("alert", function(msg) {
  alert("Triggered " + msg);
});

object.trigger("alert", "an event");
</pre>

例如，可以很方便定义一个事件调度器来协调应用程序不同地方的事件：<tt>var dispatcher = _.clone(Backbone.Events)</tt>

**on**`object.on(event, callback, [context])`<span class="alias">别名: bind</span>  
给Object绑定一个事件回调函数（**callback** ）。当事件（**event**）被触发时，回调函数被调用。 如果一个页面中有很多不同的事件，通常需要添加命名空间（使用冒号分隔）：<tt>"poll:start"</tt>，或者 <tt>"change:selection"</tt>。 事件字符串也可以是空格分隔的事件列表...

<pre>book.on("change:title change:author", ...);
</pre>

当任何事件发生时，绑定特殊的<tt>"all"</tt>事件的回调函数将被触发，函数的第一个参数为事件的名称。 例如，从一个对象到另一个的所有事件代理：

<pre>proxy.on("all", function(eventName) {
  object.trigger(eventName);
});
</pre>

所有 Backbone 事件方法也支持一种事件映射（map）语法，作为参数位置的替代：

<pre>book.on({
  "change:author": authorPane.update,
  "change:title change:subtitle": titleView.update,
  "destroy": bookView.remove
});
</pre>

最后一个可选参数[context]，可以指定回调函数被调用时内部<tt>this</tt>所在的上下文（**context**）， <tt>model.on('change', this.render, this)</tt> 或者 <tt>model.on({change: this.render}, this)</tt>。

**off**`object.off([event], [callback], [context])`<span class="alias">别名: unbind</span>  
从对象中移除先前绑定的回调函数（**callback**）。 如果没有指定上下文（[context]）参数，不同上下文所有版本的回调将被移除； 如果没有指定具体的回调（[callback]）参数，事件（**event**）的所有回调将被移除； 如果没有指定具体的事件（[event]））参数，针对所有（_all_）事件添加的回调将被移除。

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

注意：调用<tt>model.off()</tt>时，将会移除模型的所有（_all_）事件 — 包括Backbone内部用于记录的事件。

**trigger**`object.trigger(event, [*args])`  
触发指定事件（**event**）或者空格分隔的事件列表的回调函数。 后续的参数（[*args]）将会被传递给事件的回调函数。

**once**`object.once(event, callback, [context])`  
同[on](#Events-on)方法类似，区别在于绑定的回调函数在被移除前仅仅会触发一次，也就是说“当事件在下一次发生时，触发一次”。 当绑定空格分隔的多个事件列表时，回调函数将会针对每个事件触发一次，而不是仅仅针对所有列表事件触发一次。

**listenTo**`object.listenTo(other, event, callback)`  
设置对象（**object**）去监听另一个（**other**）对象所发生的某个事件。 使用这种形式的好处是，代替<tt>other.on(event, callback, object)</tt>，监听（**listenTo**）允许对象（**object**）保持对事件的跟踪，添加的所有事件在以后可以被一次性移除。 回调（**callback**）总是以对象（**object**）为执行的上下文调用。

<pre>view.listenTo(model, 'change', view.render);
</pre>

**stopListening**`object.stopListening([other], [event], [callback])`  
Either call **stopListening** with no arguments to have the **object** remove all of its [registered](#Events-listenTo) callbacks ... or be more precise by telling it to remove just the events it's listening to on a specific object, or a specific event, or just a specific callback. 设置对象（**object**）停止监听事件。 如果没有指定任何参数，将移除对象（**object**）所注册（[registered](#Events-listenTo)）的所用回调函数。 也可以指定具体的监听对象[other]、监听事件[event]或回调函数[callback]。

<pre>view.stopListening();

view.stopListening(model);
</pre>

**listenToOnce**`object.listenToOnce(other, event, callback)`  
同[listenTo](#Events-listenTo)类似，区别在于绑定的回调函数在移除前仅仅会触发一次。

**（事件目录）Catalog of Events**  
下面是Backbone内建的完整事件（包含参数）列表。 你也可以在认为合适的地方针对模型、集合和视图触发自定义事件。 <tt>Backbone</tt>对象本身混合了<tt>Events</tt>事件， 所以，可以根据应用的需要发布任何全局事件。

*   **"add"** (model, collection, options) — 当一个模型添加到集合中.
*   **"remove"** (model, collection, options) — 当一个模型从集合中移除.
*   **"update"** (collection, options) — 任何数量的模型已添加到集合或已从集合中移除后触发单一事件.
*   **"reset"** (collection, options) — 当集合的全部内容被[reset](#Collection-reset).
*   **"sort"** (collection, options) — 当集合被重新排序.
*   **"change"** (model, options) — 当模型的任意属性改变.
*   **"change:[attribute]"** (model, value, options) — 当模型的某个指定属性被改变.
*   **"destroy"** (model, collection, options) — 当模型被[destroyed](#Model-destroy).
*   **"request"** (model_or_collection, xhr, options) — 当模型或集合开始向服务器发送一个请求.
*   **"sync"** (model_or_collection, response, options) — 当模型或集合与服务器同步成功.
*   **"error"** (model_or_collection, response, options) — 当模型或集合向服务器请求失败.
*   **"invalid"** (model, error, options) — 当模型在客户端被[validation](#Model-validate)失败.
*   **"route:[name]"** (params) — 当指定的路由被匹配时，通过router触发.
*   **"route"** (route, params) — 当 _any_ 路由被匹配时，通过router触发.
*   **"route"** (router, route, params) — 当 _any_ 路由被匹配时，通过history触发.
*   **"all"** — 这是特殊的事件，针对_any_事件触发，传递事件名称作为第一个参数，其次是所有触发参数.

一般来说，当通过（<tt>model.set</tt>, <tt>collection.add</tt>, 等等...）触发一个事件回调函数时，如果你想阻止所触发的事件，你可以传递<tt>{silent: true}</tt>选项。注意这种需求不常见（_rarely_），甚至也不会遇到，但这是一个好主意，通过传递一个特殊的标记给选项，从而告诉回调忽略事件，这通常工作的更好。

## （模型）Backbone.Model

模型（**Models**）是所有应用程序的核心，包含交互数据以及大部分的逻辑：转换（conversions），验证（validations），计算属性（computed properties）和访问控制（access control）。 你可以通过扩展**Backbone.Model** 来定义特定领域（domain-specific）的方法，模型（**Model**）提供管理变化的基础功能集。

下面是一个假设的例子，但是它展示了模型中的自定义方法，通过设置颜色属性来触发改变颜色事件。 运行一次代码，输入颜色值确定后，<tt>sidebar</tt>的背景将会变为你输入的颜色，你可以试一下看看。

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
可以通过扩展（**Backbone.Model**）来创建一个模型（**Model**）类，并添加实例属性（**instanceProperties**），也可以选择性的为构造函数直接添加类属性（**classProperties**）。

扩展（**extend**）正确设置了原型链，所以通过**extend**创建的子类，可以被进一步扩展和派生，只要你喜欢。

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

关于<tt>super</tt>的简要说明：Javascript没有提供一个简单的方法去调用super — 在原型链上定义的同名函数。 如果你覆盖了核心方法，如<tt>set</tt>或<tt>save</tt>， 想去调用父对象的实现，你需要明确调用，向下面这样：

<pre>var Note = Backbone.Model.extend({
  set: function(attributes, options) {
    Backbone.Model.prototype.set.apply(this, arguments);
    ...
  }
});
</pre>

**constructor / initialize**`new Model([attributes], [options])`  
当创建一个模型的一个实例，你可以传递**attributes**的初始值，通过[set](#Model-set)作用到模型中。 如果你定义一个**initialize**初始化方法，它将在模型被创建后调用。

<pre>new Book({
  title: "One Thousand and One Nights",
  author: "Scheherazade"
});
</pre>

很少情况下，如果你想要得到，你可以覆盖构造函数**constructor**来替换默认的模型构造函数。

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

如果你传递一个<tt>{collection: ...}</tt>作为选项**options**，模型会获得一个<tt>collection</tt>属性， 用来表明模型归属的集合，它也用来帮助计算模型的[url](#Model-url)。 当首次添加模型到集合中的时候，<tt>model.collection</tt>属性通常会被自动创建。 注意相反则不正确，给构造函数传递这个选项并不会自动添加模型到集合中。有时候，有用。

如果传递<tt>{parse: true}</tt>选项，**attributes**在通过[set](#Model-set)到模型之前 首先会被[parse](#Model-parse)转变。

**get**`model.get(attribute)`  
获取模型一个属性的当前值，例如：<tt>note.get("title")</tt>

**set**`model.set(attributes, [options])`  
设置模型的一个或多个hash属性。如果任意属性改变了模型的状态，将会触发模型的<tt>"change"</tt>事件。 也可以触发某个具体属性的change事件，你也可以绑定到这些，例如： <tt>change:title</tt>, 和 <tt>change:content</tt>。 你也可以传递个别的键和值。

<pre>note.set({title: "March 20", content: "In his eyes she eclipses..."});

book.set("title", "A Scandal in Bohemia");
</pre>

**escape**`model.escape(attribute)`  
类似[get](#Model-get)，但是返回HTML转义后的模型属性。 如果你将插值数据从模型转换为HTML，使用**escape**获取属性，可以阻止[XSS](http://en.wikipedia.org/wiki/Cross-site_scripting)攻击。

<pre class="runnable">var hacker = new Backbone.Model({
  name: "<script>alert('xss')</script>"
});

alert(hacker.escape('name'));
</pre>

**has**`model.has(attribute)`  
如果属性被设置为非空non-null或非未定义non-undefined，返回<tt>true</tt>。

<pre>if (note.has("title")) {
  ...
}
</pre>

**unset**`model.unset(attribute, [options])`  
通过从内部哈希属性中删除来移除一个属性。 触发一个<tt>"change"</tt>事件，除非传递了<tt>silent</tt>选项。

**clear**`model.clear([options])`  
移除模型的所有属性，包括<tt>id</tt>属性。 触发一个<tt>"change"</tt>事件，除非传递了<tt>silent</tt>选项。

**id**`model.id`  
模型的一个特殊属性，**id**是一个任意的字符串（整数id或UUID）。 如果你在属性哈希中设置**id**，它会被复制到模型中作为直接属性。 通过id可以从集合中获取模型，id被用来生成模型默认的URLS。

**idAttribute**`model.idAttribute`  
一个存储在id属性下的模型的唯一标识符。 如果你直接与后端通信（CouchDB,MongoDB）,使用一个不同的唯一key,你可以设置模型的<tt>idAttribute</tt>来转换key到id的映射。

<pre class="runnable">var Meal = Backbone.Model.extend({
  idAttribute: "_id"
});

var cake = new Meal({ _id: 1, name: "Cake" });
alert("Cake id: " + cake.id);
</pre>

**cid**`model.cid`  
模型的一个特殊属性，当模型首次被创建的时候，**cid**或者客户端id作为一个唯一标识符自动分配给模型。 当模型没有被保存到服务器，还没有最终真正的id，但是已经需要在UI中显示的时候，客户端ids则非常方便。

**attributes**`model.attributes`  
**attributes**属性是包含模型状态的内部哈希 — 通常是一个（但不是必须）JSON对象的形式来表示服务器端模型的数据。 它通常是数据库一行数据的简单序列化，但它也可以是客户端client-side的计算状态。

请使用[set](#Model-set)来更新**attributes**，以代替直接修改它们。 如果你想去获取和munge一个模型属性的副本，使用<tt>_.clone(model.attributes)</tt>来代替。

注意：[Events](#Events)接受空格分隔的事件列表，但是属性名称不应该包含空格。

**changed**`model.changed`  
**changed**属性是内部哈希包含自最后一次[set](#Model-set)以来所有changed的属性。 请不要直接更新**changed**属性，因为它的状态是内部通过[set](#Model-set)维护的。 一个**changed**副本可以通过[changedAttributes](#Model-changedAttributes)获得。

**defaults**`model.defaults or model.defaults()`  
**defaults**哈希（或函数）被用来指定模型默认的属性。当创建模型的一个实例的时候， 任何未被设置的属性将被设置为它们的默认属性值。

<pre class="runnable">var Meal = Backbone.Model.extend({
  defaults: {
    "appetizer":  "caesar salad",
    "entree":     "ravioli",
    "dessert":    "cheesecake"
  }
});

alert("Dessert will be " + (new Meal).get('dessert'));
</pre>

记住在JavaScript中，对象是按引用传递的，因此，如果你包括一个对象作为默认值，它将在所有实例中分享。 可以定义**defaults**为一个函数来代替。

**toJSON**`model.toJSON([options])`  
返回JSON字符串化模型属性[attributes](#Model-attributes)的浅副本。 它可以用来持久化，序列化或者作为发送到服务器前的增强。 这个方法名称让人有点困惑，它实际并不返回一个JSON字符串 — 但是我担心它的方式是[JavaScript API for **JSON.stringify**](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#toJSON_behavior)的工作。

<pre class="runnable">var artist = new Backbone.Model({
  firstName: "Wassily",
  lastName: "Kandinsky"
});

artist.set({birthday: "December 16, 1866"});

alert(JSON.stringify(artist));
</pre>

**sync**`model.sync(method, model, [options])`  
使用[Backbone.sync](#Sync)来与服务器同步模型状态，可以使用自定义行为来覆盖。

**fetch**`model.fetch([options])`  
代理[Backbone.sync](#Sync)方法来与从服务器抓取的属性合并模型状态。 返回一个 [jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR)对象。 对于如果模型从未有过数据，或者如果想去确保你有最新的服务器状态非常有用。 如果服务器状态和当前的属性有区别，将触发一个<tt>"change"</tt>事件。 <tt>fetch</tt>选项哈希接受<tt>success</tt>和<tt>error</tt>回调，均传递<tt>(model, response, options)</tt>作为参数。

<pre>// Poll every 10 seconds to keep the channel model up-to-date.
setInterval(function() {
  channel.fetch();
}, 10000);
</pre>

**save**`model.save([attributes], [options])`  
通过[Backbone.sync](#Sync)代理请求来保存一个模型到数据库中（或者替代的持久化层）。 如果验证成功，返回一个[jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR)对象，否则返回<tt>false</tt>。 **attributes**哈希(通过[set](#Model-set))应该包含你想去改变的属性 — 没有提及的keys不会被改变 — 但是资源的一个_complete representation_ 将会发送到服务器。 通过<tt>set</tt>，你可以传递个别的键值对来代替一个哈希。 如果模型有一个[validate](#Model-validate)方法，并且验证失败，模型不会被保存。 如果模型是[isNew](#Model-isNew)，保存方法会是一个<tt>"create"</tt>（HTTP <tt>POST</tt>）请求， 如果模型已经存在，保存方法将会是一个<tt>"update"</tt> (HTTP <tt>PUT</tt>)请求。

如果你仅仅想要将_changed_属性发送到服务器，调用<tt>model.save(attrs, {patch: true})</tt>。 你将连同传递的属性参数发送一个<tt>PATCH</tt>请求到服务器。

连同新属性调用<tt>save</tt>方法将立即触发一个<tt>"change"</tt>事件， 一个<tt>"request"</tt>事件作为向服务器Ajax请求的开始， 一个<tt>"sync"</tt>事件在服务器确认成功改变后触发。 如果你想去在设置模型新属性之前等待服务器的响应， 传递<tt>{wait: true}</tt>参数。

下面的例子，注意我们覆盖了<tt>Backbone.sync</tt>版本，首次接收一个<tt>"create"</tt>请求保存模型和第二次的一个<tt>"update"</tt>请求。

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

**save**方法的属性哈希接收<tt>success</tt> 和 <tt>error</tt>回调，传递<tt>(model, response, options)</tt>参数。 如果服务端验证失败，返回一个非<tt>200</tt>HTTP响应码，和一个响应错误文本或者JSON。

<pre>book.save("author", "F.D.R.", {error: function(){ ... }});
</pre>

**destroy**`model.destroy([options])`  
通过[Backbone.sync](#Sync)代理一个HTTP <tt>DELETE</tt>请求来销毁服务器上的模型。 返回一个[jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR)对象，或者如果模型[isNew](#Model-isNew) 则返回<tt>false</tt>。 选项哈希中接收<tt>success</tt>和<tt>error</tt>回调，传递<tt>(model, response, options)</tt>参数。 在模型上触发一个<tt>"destroy"</tt>事件，将冒泡到包含它的任意集合中。 一个<tt>"request"</tt>事件作为向服务器Ajax请求的开始， 一个<tt>"sync"</tt>事件当服务器确认成功删除之后触发。 如果你想在从集合中移除模型之前等待服务器的响应，传递<tt>{wait: true}</tt>选项。

<pre>book.destroy({success: function(model, response) {
  ...
}});
</pre>

**Underscore Methods (9)**  
**Backbone.Model**使用 Backbone 代理**Underscore.js**的9个对象函数。 这儿没有它们所有的文档，但你可以查看完整细节的Underscore文档 …

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
这个方法默认未定义，你可以用任何可以在JavaScript中执行的自定义业务逻辑来覆盖它。 默认，<tt>save</tt>方法在设置任意属性之前会检查**validate**的结果， 但是你也可以为<tt>set</tt>方法选项哈希传递<tt>{validate: true}</tt>参数来验证新的属性。  
**validate**方法接收传递给<tt>set</tt>或者<tt>save</tt>的模型属性和选项哈希。 如果属性通过验证，**validate**不返回任何值；如果它们验证失败，返回一个你选择的错误。 它可以是一个简单的字符串错误消息显示，或者一个完整的描述错误过程的错误对象。 如果**validate**返回一个错误，<tt>save</tt>方法不会继续，服务器中的模型属性不会被修改。 验证失败触发一个<tt>"invalid"</tt>事件，用方法的返回值来设置模型的<tt>validationError</tt>属性。

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

<tt>"invalid"</tt>事件用于在模型或集合级别提供粗粒度的错误消息。

**validationError**`model.validationError`  
[validate](#Model-validate)最后失败验证的返回值。

**isValid**`model.isValid()`  
运行[validate](#Model-validate)来检查模型的状态。

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
返回模型资源位于服务器上位置的相对URL。 如果你的模型位于其它地方，用正确的逻辑覆盖此方法。 生成URLs的形式为：<tt>"[collection.url]/[id]"</tt>。 默认，在不考虑模型集合的情况下，你可以指定一个确切的<tt>urlRoot</tt>来覆盖。

代理[Collection#url](#Collection-url)来生成URL，因此确保你已经定义，或者如果所有模型分享一个共用的root URL， 可以定义一个[urlRoot](#Model-urlRoot)属性。 一个模型和一个id<tt>101</tt>，存储在一个<tt>url</tt>为<tt>"/documents/7/notes"</tt>的[Backbone.Collection](#Collection)中， 将拥有这样的URL：<tt>"/documents/7/notes/101"</tt>。

**urlRoot**`model.urlRoot or model.urlRoot()`  
如果你在集合的外部_outside_使用模型，指定一个<tt>urlRoot</tt>来允许默认的[url](#Model-url)方法基于模型的id生成URLs。 <tt>"[urlRoot]/id"</tt>  
通常，你不需要定义这个。注意<tt>urlRoot</tt>也可以是一个函数。

<pre class="runnable">var Book = Backbone.Model.extend({urlRoot : '/books'});

var solaris = new Book({id: "1083-lem-solaris"});

alert(solaris.url());
</pre>

**parse**`model.parse(response, options)`  
用[fetch](#Model-fetch)和[save](#Model-save)方法，当模型的数据从服务器返回的时候， **parse**会被调用。 这个方法传递原始的<tt>response</tt>对象和应该被[set](#Model-set)到模型的属性哈希。 默认实现是一个空操作（no-op），仅仅通过JSON响应。 如果需要和现有的API一起工作，或者响应你的更好的命名空间，覆盖此方法。

如果你使用Rails3.1之前的版本作为后端，你注意它的默认<tt>to_json</tt>实现包含在命名空间下的模型属性。 不允许Backbone的这个无缝整合行为，设置：

<pre>ActiveRecord::Base.include_root_in_json = false
</pre>

**clone**`model.clone()`  
返回一个包含相同属性的模型新实例。

**isNew**`model.isNew()`  
这个模型已经保存到服务器了吗？ 如果模型还没有一个<tt>id</tt>，它被认为是新的。

**hasChanged**`model.hasChanged([attribute])`  
自从最后[set](#Model-set)以来模型是否改变了？ 如果传递一个**attribute**，如果指定的属性改变了则返回<tt>true</tt>。

注意此方法，和下面的变化有关，仅仅在 <tt>"change"</tt> 事件期间有用。

<pre>book.on("change", function() {
  if (book.hasChanged("title")) {
    ...
  }
});
</pre>

**changedAttributes**`model.changedAttributes([attributes])`  
获取自上一次[set](#Model-set)以来发生改变的模型属性的哈希，如果没有则返回<tt>false</tt>。 也可以传递一个额外的**attributes**哈希属性，返回改变的模型属性哈希。 这也可以用来解决一个视图的部分应该被更新，或者调用需要与服务器的同步更改。

**previous**`model.previous(attribute)`  
在 <tt>"change"</tt>事件期间，此方法可以用来获取属性被改变之前的值。

<pre class="runnable">var bill = new Backbone.Model({
  name: "Bill Smith"
});

bill.on("change:name", function(model, name) {
  alert("Changed name from " + bill.previous("name") + " to " + name);
});

bill.set({name : "Bill Jones"});
</pre>

**previousAttributes**`model.previousAttributes()`  
返回模型改变之前的属性副本。对于比较不同版本的模型，或者在错误产生后恢复到一个有效状态时非常有用。

## 集合（Backbone.Collection）

集合是排序后的模型集。你可以绑定<tt>"change"</tt>事件来监听集合中任何模型的修改， 监听<tt>"add"</tt>和 <tt>"remove"</tt>事件和从服务器<tt>fetch</tt>集合，以及 可使用完整的[Underscore.js methods](#Collection-Underscore-Methods)。

为了方便，集合中的模型触发的任何事件也将在直接在集合中触发。 这允许你去监听集合中的任何模型的特殊属性更改，例如： <tt>documents.on("change:selected", ...)</tt>

**extend**`Backbone.Collection.extend(properties, [classProperties])`  
通过扩展 **Backbone.Collection**来创建一个**Collection**类，并提供实例属性**properties**，也可以 在集合的构造函数上直接添加**classProperties**类属性。

**model**`collection.model([attrs], [options])`  
覆盖整个属性来指定集合所包含的模型类。 如果定义，你可以传递原始属性对象（和数组）来[add](#Collection-add)，[create](#Collection-create)， 和[reset](#Collection-reset)，属性会被转换为模型合适的属性。

<pre>var Library = Backbone.Collection.extend({
  model: Book
});
</pre>

用返回模型的构造函数来覆盖这个属性，可以使一个集合包含多态模型。

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
覆盖这个方法的返回值，集合用来根据给定的属性识别模型。 对于将包含不同[<tt>idAttribute</tt>](#Model-idAttribute)值的多个表，组合到单个集合中，非常有用。

默认，使用集合中模型类的[<tt>idAttribute</tt>](#Model-idAttribute)属性值，如果失败，使用<tt>id</tt>。 如果你的集合使用一个模型工厂[model factory](#Collection-model)，这些模型的<tt>idAttribute</tt>和<tt>id</tt>不一样， 你必须覆盖此方法。

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
当创建一个集合的时候，你可以选择传递初始化模型**models**数组。 集合的比较[comparator](#Collection-comparator)属性也可以作为一个选项包含。 传递<tt>false</tt>作为比较选项将会阻止集合排序。 如果你定义一个初始化**initialize**方法，当集合创建后它将被调用。 这2个选项，如果提供将直接附加给集合：<tt>model</tt> 和 <tt>comparator</tt>。  
给<tt>models</tt>选项传递<tt>null</tt>来创建一个包含<tt>options</tt>的空集合。

<pre>var tabs = new TabSet([tab1, tab2, tab3]);
var spaces = new Backbone.Collection(null, {
  model: Space
});
</pre>

**models**`collection.models`  
返回集合中模型数组的原始访问（Raw access）。 通常你可以使用<tt>get</tt>，<tt>at</tt>或者**Underscore methods**方法来访问模型对象， 但是偶尔也需要直接引用数组。

**toJSON**`collection.toJSON([options])`  
返回集合中每个模型属性（通过[toJSON](#Model-toJSON)）哈希的数组。 这可以被用来作为一个整体序列化和持久化集合。方法名称可能会有一点困惑，因为它类似 [JavaScript's JSON API](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#toJSON_behavior).

<pre class="runnable">var collection = new Backbone.Collection([
  {name: "Tim", age: 5},
  {name: "Ida", age: 26},
  {name: "Rob", age: 55}
]);

alert(JSON.stringify(collection));
</pre>

**sync**`collection.sync(method, collection, [options])`  
使用 [Backbone.sync](#Sync)方法来向服务器持久化集合的状态。可以被自定义行为覆盖。

**Underscore Methods (46)**  
Backbone 代理**Underscore.js**提供的46迭代方法来操作**Backbone.Collection**。 这儿不包含所有文档，但是你可以查看Undersore文档来了解完整细节…

大多数方法可以带一个对象或者字符串来支持model-attribute-style断言或者一个接收模型实例作为参数的函数。

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
添加一个模型（或一个模型数组）到集合中，每个模型将会触发一个<tt>"add"</tt>事件，随后再触发一个 <tt>"update"</tt>事件。 如果集合定义了[model](#Collection-model)属性，你也可以传递原始属性对象，它们将被作为模型的实例。 返回添加的（或者已存在的，如果复制）模型。 传递<tt>{at: index}</tt>来在特殊的索引<tt>index</tt>位置拼接集合中模型。 如果你添加已存在_already_的模型到集合中，将会被忽略。除非你传递<tt>{merge: true}</tt>选项，这将合并属性到相应的模型中， 触发任何合适的<tt>"change"</tt>事件。

<pre class="runnable">var ships = new Backbone.Collection;

ships.on("add", function(ship) {
  alert("Ahoy " + ship.get("name") + "!");
});

ships.add([
  {name: "Flying Dutchman"},
  {name: "Black Pearl"}
]);
</pre>

注意：添加同一个模型（包含相同的<tt>id</tt>）到集合中超过一次将是一个空操作（no-op）。

**remove**`collection.remove(models, [options])`  
从集合中移除一个模型（或模型数组），返回被移除的模型或模型数组。 模型可以是一个模型实例，一个<tt>id</tt>字符串或一个JS对象，可被[<tt>collection.get</tt>](#Collection-get) 方法当作<tt>id</tt>参数接受的任何值均可以。 每个模型将触发一个<tt>"remove"</tt>事件，和随后触发的单个<tt>"update"</tt>事件，除非传递<tt>{silent: true}</tt>参数。 模型的索引在被移除之前可作为<tt>options.index</tt>传递给监听器。

**reset**`collection.reset([models], [options])`  
一次添加或移除一个模型当然好，但是有时要处理很多的模型，你可能就想去批量更新集合。 使用**reset**连同一个新的模型列表（或者属性哈希）来替换一个集合，完成时触发单个<tt>"reset"</tt>事件， 不会_without_触发所有模型上的任何添加和移除事件，返回新设置newly-set的模型。 为了方便起见，<tt>"reset"</tt>事件期间，之前的任何模型列表，可作为<tt>options.previousModels</tt>属性。  
给<tt>models</tt>选项传递<tt>null</tt>（连同<tt>options</tt>）来清空集合。

这儿有一个例子在初始化页面期间，使用**reset**来启动一个集合，一个Rails的应用：

<pre><script>
  var accounts = new Backbone.Collection;
  accounts.reset(<%= @accounts.to_json %>);
</script>
</pre>

调用 <tt>collection.reset()</tt>，不传递任何模型作为参数将清空整个集合。

**set**`collection.set(models, [options])`  
**set**方法执行一个“智能”更新集合（连同传递的模型列表）。 如果参数列表中的模型不在集合中则添加；如果模型已经存在则将它的属性合并到模型中；如果集合中的模型不在_aren't_参数列表的模型中，它们会被移除。 所有相关的<tt>"add"</tt>，<tt>"remove"</tt>， 和 <tt>"change"</tt>事件被触发。 返回集合中修改后touched的模型。 如果你想去自定义行为，可以通过选项来禁止：<tt>{add: false}</tt>，<tt>{remove: false}</tt>或<tt>{merge: false}</tt>。

<pre>var vanHalen = new Backbone.Collection([eddie, alex, stone, roth]);

vanHalen.set([eddie, alex, stone, hagar]);

// Fires a "remove" event for roth, and an "add" event for "hagar".
// Updates any of stone, alex, and eddie's attributes that may have
// changed over the years.
</pre>

**get**`collection.get(id)`  
Get a model from a collection, specified by an [id](#Model-id), a [cid](#Model-cid), or by passing in a **model**. 获取集合中的一个模型，指定一个[id](#Model-id)，一个[cid](#Model-cid)，或者一个**model**。

<pre>var book = library.get(110);
</pre>

**at**`collection.at(index)`  
通过指定索引来获取集合中的模型。 对于已排序的集合或者没有排序的集合，**at**仍然可以按照插入的顺序获取模型。 传递一个负值索引，将从集合的尾部开始获取模型。

**push**`collection.push(model, [options])`  
添加一个模型到集合的尾部，options选项参数与[add](#Collection-add)方法相同。

**pop**`collection.pop([options])`  
移除并返回集合中的最后一个模型。options选项参数与[remove](#Collection-remove)方法相同。

**unshift**`collection.unshift(model, [options])`  
添加一个模型到集合中的开始出。options选项参数与[add](#Collection-add)方法相同。

**shift**`collection.shift([options])`  
移除并返回集合中的第一个模型。options选项参数与[remove](#Collection-remove)方法相同。

**slice**`collection.slice(begin, end)`  
返回集合中模型的浅副本，options参数选项同数组原生的slice方法。 [Array#slice](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/slice).

**length**`collection.length`  
类似数组，集合维护一个长度<tt>length</tt>属性，统计它所包含的模型的数量。

**comparator**`collection.comparator`  
默认集合不包含**comparator**。 如果你定义了一个comparator，它将被用来维护集合的排序规则。 也就是说，添加模型，他们会被插入到<tt>collection.models</tt>的正确索引位置。 一个comparator可以定义为一个[sortBy](http://underscorejs.org/#sortBy)（一个带有单个参数的函数）， 或者一个 [sort](https://developer.mozilla.org/JavaScript/Reference/Global_Objects/Array/sort)（一个带有2个参数的比较函数）， 或者是一个字符串来指示属性如何去排序。

"sortBy" comparator函数带有一个模型为参数，返回模型相对于其它模型如何排序的一个数值或字符串值。 "sort" comparator函数带有二个模型为参数，如果第一个模型需要排在第二个模型的前面，返回<tt>-1</tt>； 如果它们具有相同的级别，返回<tt>0</tt>；如果第一个模型需要排在第二个模型的后面则返回<tt>1</tt>。 _注意：Backbone使用哪种风格取决于比较函数的参数数量，因此要小心，如果你的comparator函数已被绑定(is bound)。_

注意下面例子中，即使用相反的顺序添加chapters，集合中它们依然保持正确的顺序：

<pre class="runnable">var Chapter  = Backbone.Model;
var chapters = new Backbone.Collection;

chapters.comparator = 'page';

chapters.add(new Chapter({page: 9, title: "The End"}));
chapters.add(new Chapter({page: 5, title: "The Middle"}));
chapters.add(new Chapter({page: 1, title: "The Beginning"}));

alert(chapters.pluck('title'));
</pre>

如果你以后更改模型属性的话，带有comparator的集合不会自动排序，因此你需要在更改模型属性之后调用<tt>sort</tt>方法来重新排序。

**sort**`collection.sort([options])`  
强制集合自身重新排序。正常情况下，你不需要调用这个方法， 集合会在添加模型的时候使用[comparator](#Collection-comparator)来排序自身。 传递<tt>{sort: false}</tt>参数给<tt>add</tt>方法，可以禁止添加模型时排序。 调用**sort**将触发集合的<tt>"sort"</tt>事件。

**pluck**`collection.pluck(attribute)`  
摘取集合中模型的一个属性。 相当于调用<tt>map</tt>方法，迭代后返回单个属性。

<pre class="runnable">var stooges = new Backbone.Collection([
  {name: "Curly"},
  {name: "Larry"},
  {name: "Moe"}
]);

var names = stooges.pluck("name");

alert(JSON.stringify(names));
</pre>

**where**`collection.where(attributes)`  
返回集合中的模型属性匹配**attributes**的一个模型数组，对于简单过滤<tt>filter</tt>非常有用。

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
类似[where](#Collection-where)，但是仅仅返回集合中的模型属性匹配**attributes**的第一个模型。

**url**`collection.url or collection.url()`  
设置**url**属性（或函数）来引用集合在服务器上的位置。 集合中的模型将使用集合的**url**来构建它们自身的URLs。

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
通过[fetch](#Collection-fetch)方法，当集合的模型从服务器返回时**parse**被调用。 函数被传递原始的<tt>response</tt>对象参数，其中包含要添加[added](#Collection-add)到集合的模型属性数组。 默认实现是空操作（no-op），仅仅通过JSON响应。 如果你想同现有的API一起工作，或者响应更好的命名空间，可以覆盖这个方法。

<pre>var Tweets = Backbone.Collection.extend({
  // The Twitter Search API returns tweets under "results".
  parse: function(response) {
    return response.results;
  }
});
</pre>

**clone**`collection.clone()`  
Returns a new instance of the collection with an identical list of models. 返回包含相同模型列表的集合新实例。

**fetch**`collection.fetch([options])`  
从服务器抓取集合的默认模型集，当它们可用时[setting](#Collection-set)到集合中。 选项**options**哈希包含成功<tt>success</tt>和失败<tt>error</tt>回调，携带<tt>(collection, response, options)</tt>参数。 当模型数据从服务器返回时，它使用[set](#Collection-set)方法来（智能）合并抓取到模型， 除非传递了<tt>{reset: true}</tt>选项，这将导致集合将被（有效率的）重置[reset](#Collection-reset)。 代理[Backbone.sync](#Sync)方法自定义后台的持久性策略， 并返回一个 [jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR)对象。 服务器处理**fetch**请求，应该返回一个JSON模型数组。

<pre class="runnable">Backbone.sync = function(method, model) {
  alert(method + ": " + model.url);
};

var accounts = new Backbone.Collection;
accounts.url = '/accounts';

accounts.fetch();
</pre>

**fetch**的行为可以被自定义，通过使用可用于[set](#Collection-set)的选项。 例如，抓取一个集合，对于每个新增的模型获取一个<tt>"add"</tt>事件，对于每个现有模型的更改而获取的一个<tt>"change"</tt>事件， 通过<tt>collection.fetch({remove: false})</tt>来设置不移除任何东西。

**jQuery.ajax**选项也可以直接传递给**fetch**选项， 因此可以抓取分页集合的一个特殊页面： <tt>Documents.fetch({data: {page: 3}})</tt>

注意：**fetch**不应该被用来在页面加载时生成集合 — 所有模型需要在加载时间应该已经引导[bootstrapped](#FAQ-bootstrap)到另一个地方。 **fetch**意在针对那些不需要立即使用的惰性加载lazily-loading模型界面：例如，笔记集合文档可以被打开和关闭。

**create**`collection.create(attributes, [options])`  
给集合添加模型新实例的快捷方式。 等同于用哈希属性来实例化模型，保存模型到服务器，服务器改成创建后添加模型到集合中，返回新的模型。 如果客户端验证失败，模型将不会被保存，产生验证错误消息。 使用这个方法的前提是，你应该设置集合的模型[model](#Collection-model)属性。 **create**方法不但可以接受一个属性哈希，还可以是一个已存在，未保存的模型对象。

创建一个模型将会立即触发集合的<tt>"add"</tt>事件，一个添加新模型<tt>"request"</tt>事件被发送到服务器， 一旦服务器创建模型成功后响应，将会触发一个<tt>"sync"</tt>事件。

<pre>var Library = Backbone.Collection.extend({
  model: Book
});

var nypl = new Library;

var othello = nypl.create({
  title: "Othello",
  author: "William Shakespeare"
});
</pre>

## 路由（Backbone.Router）

web应用程序常常需要为重要的位置提供链接，书签，分享URLs。 直到最近，哈希片段（<tt>#page</tt>）被用来提供这些永久链接，但随着History API的到来，现在可以使用标准URLs（<tt>/page</tt>）。 **Backbone.Router**提供方法来路由客户端页面，将它们连接到动作（actions）和事件（events）。 对于还不支持History API的浏览器来说，Router优雅处理回退，并透明转换URL的片段版本。

页面加载期间，你的应用程序在创建完成它所有的路由之后，一定要调用<tt>Backbone.history.start()</tt>或者 <tt>Backbone.history.start({pushState: true})</tt>来路由初始网址。

**extend**`Backbone.Router.extend(properties, [classProperties])`  
开始创建一个自定义路由类。定义当某段URL片段被匹配时应触发的动作，提供路由和动作对的路由[routes](#Router-routes)哈希。 注意：你应该避免在路由定义时使用斜杠开头。

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
路由哈希映射带有参数的URLs和相应处理函数名称（如果你喜欢，也可以直接定义函数）， 类似视图的[View](#View)事件哈希[events hash](#View-delegateEvents)。 路由可以包含参数部分，<tt>:param</tt>，在斜杠之间匹配一个单独URL组件；<tt>*splat</tt>，将匹配任意数量的URL组件。 一个路由部分可以通过圆括号来定义可选项 <tt>(/:optional)</tt>。

例如，一个<tt>"search/:query/p:page"</tt>路由将匹配一个<tt>#search/obama/p2</tt>片段， 传递<tt>"obama"</tt>和<tt>"2"</tt>给动作（action）。

一个<tt>"file/*path"</tt>路由将匹配<tt>#file/folder/file.txt</tt>，传递 <tt>"folder/file.txt"</tt>给动作（action）。

一个<tt>"docs/:section(/:subsection)"</tt>路由将匹配<tt>#docs/faq</tt>和<tt>#docs/faq/installing</tt>， 第一个例子中传递<tt>"faq"</tt>给动作（action），第二个传递<tt>"faq"</tt> 和 <tt>"installing"</tt>给动作（action）。

一个嵌套的可选路由<tt>"docs(/:section)(/:subsection)"</tt>将匹配<tt>#docs</tt>，<tt>#docs/faq</tt>和<tt>#docs/faq/installing</tt>， 第二个例子中传递<tt>"faq"</tt>给动作（action），第三个中传递<tt>"faq"</tt>和<tt>"installing"</tt>给动作（action）。

末尾斜杠也会作为URL部分被处理，当访问时，作为一个唯一路由（正确）处理。 <tt>docs</tt>和<tt>docs/</tt>将触发不同的回调。 如果你不能避免需要生成这两种类型URLs，你可以定义一个<tt>"docs(/)"</tt>匹配器来捕捉这两种情况。

当用户点击返回按钮，或者输入一个URL，匹配到一个特殊的路由，动作名称将被作为一个事件[event](#Events)触发。 因此，其它对象可以监听路由，并可以被通知。 下面的例子中，浏览<tt>#help/uploading</tt>将触发一个路由<tt>route:help</tt>事件。

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
当创建一个新的路由器，你可以选择直接传递[routes](#Router-routes)哈希作为选项， 也可以定义<tt>initialize</tt>方法，将所有<tt>options</tt>传递给它。

**route**`router.route(route, name, [callback])`  
手动为路由器创建一个路由，<tt>route</tt>参数可以是一个路由字符串[routing string](#Router-routes)或者 正则表达式。 每一个匹配捕获的路由或者正则表达式将作为参数传递给回调函数。 <tt>name</tt>参数每当路由匹配时，将作为<tt>"route:name"</tt>事件被触发。 如果省略参数<tt>callback</tt>，将使用<tt>router[name]</tt>代替。 后面添加的路由可以覆盖之前声明的路由。

<pre>initialize: function(options) {

  // Matches #page/10, passing "10"
  this.route("page/:number", "page", function(number){ ... });

  // Matches /117-a/b/c/open, passing "117-a/b/c" to this.open
  this.route(/^(.*?)\/open$/, "open");

},

open: function(id) { ... }
</pre>

**navigate**`router.navigate(fragment, [options])`  
每当你到达应用程序的某个地方，想存为URL，可调用**navigate**来更新URL。 如果想去调用路由函数，设置**trigger**选项为<tt>true</tt>。 要更新URL而在浏览器历史记录不创建记录，设置**replace**选项为<tt>true</tt>。

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
这个方法在路由器内部被调用，每当一个路由被匹配，相应的回调**callback**即被执行。 返回**false**来取消当前转换的执行。 覆盖它来执行自定义解析或路由包装，例如，在将查询字符串传递给路由回调之前解析它们，像这样：

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

## 历史记录（Backbone.history）

**History**服务作为一个全局路由（每帧）来处理<tt>hashchange</tt>事件或<tt>pushState</tt>， 匹配相应的路由，并触发回调。 你不需要自己创建它们，因为<tt>Backbone.history</tt>已经包含了。

**pushState**支持在现有功能基础上的一个可选项。 旧版本浏览器不支持<tt>pushState</tt>，将继续使用哈希（hash-based）URL片段， 如果一个哈希URL被一个支持<tt>pushState</tt>的浏览器浏览，它将被透明升级为真实的URL。 注意使用真实URLs需要你的web服务器可以正确渲染这些页面，因此改变后端（back-end）同样是必需的。 例如，如果你有一个路由<tt>/documents/100</tt>，浏览器直接浏览这个URL时，你的web服务器必须可以服务这个页面。 对于搜索引擎检索，最好是服务器生成完整的HTML页面...但是如果一个应用程序，仅仅通过根目录（root url）来渲染相同的内容，通过 Backbone视图（Views）和脚本（JavaScript）来填充剩余的部分，也可以正常工作。

**start**`Backbone.history.start([options])`  
当你所有的[Routers](#Router)创建完成，并且所有的路由已设置正确，调用<tt>Backbone.history.start()</tt> 来开始监控<tt>hashchange</tt>事件，并调度路由。 后面再调用<tt>Backbone.history.start()</tt>将抛出一个错误。 <tt>Backbone.History.started</tt>是一个布尔值来指示是否已经被调用。

想要在应用中使用HTML5的<tt>pushState</tt>功能，可使用<tt>Backbone.history.start({pushState: true})</tt>。 如果想去使用<tt>pushState</tt>，但是有些浏览器原生不支持它，可使用整页刷新代替，你可以添加<tt>{hashChange: false}</tt>选项。

如果你的应用不是从域名根目录（root url）开始服务，需要告诉History根目录的具体位置， 作为一个选项：<tt>Backbone.history.start({pushState: true, root: "/public/search/"})</tt>

当调用时，如果一个路由成功匹配当前URL，<tt>Backbone.history.start()</tt>返回<tt>true</tt>。 如果未定义路由匹配当前URL，则返回<tt>false</tt>。

如果服务器已完成渲染整个页面，你不想去当历史记录开始时触发初始化路由，传递<tt>silent: true</tt>。

因为哈希（hash-based）历史记录在IE中依赖<tt><iframe></tt>，因此仅可以在DOM加载完毕后调用<tt>start()</tt>。

<pre>$(function(){
  new WorkspaceRouter();
  new HelpPaneRouter();
  Backbone.history.start({pushState: true});
});
</pre>

## （同步）Backbone.sync

**Backbone.sync**是一个方法，Backbone每次尝试从服务器去读取（read）或保存（save）模型时调用。 默认，它使用<tt>jQuery.ajax</tt>来生成一个RESTful JSON 请求， 并返回一个[jqXHR](http://api.jquery.com/jQuery.ajax/#jqXHR).对象。 你可以使用一个不同的持久化策略来覆盖它，例如WebSockets，XML transport，或者 Local Storage。

**Backbone.sync**的方法签名是<tt>sync(method, model, [options])</tt>

*   **method** - CRUD方法（<tt>"create"</tt>, <tt>"read"</tt>, <tt>"update"</tt>, 或 <tt>"delete"</tt>）
*   **model** – 被保存的模型 (或者要读取的集合)
*   **options** – 成功和错误回调，以及jQuery的所有其它请求选项。

默认情况下，当**Backbone.sync**发送一个请求去保存一个模型，传递的属性被序列化为JSON， 发送的HTTP报文包含请求的内容类型content-type为<tt>application/json</tt>。 当返回一个JSON响应时，获取到的模型属性已被服务器更改，需要在客户端进行更新。 当响应一个集合的读取<tt>"read"</tt>请求（[Collection#fetch](#Collection-fetch)）时，将获取一个模型属性对象的数组。

每当一个模型或集合开始与服务器同步**sync**时，发出一个请求<tt>"request"</tt>事件。 如果请求成功响应，触发一个同步<tt>"sync"</tt>事件，如果失败，触发<tt>"error"</tt>事件。

**sync**方法可以被<tt>Backbone.sync</tt>全局覆盖， 或者在细粒度级别，为Backbone的集合或模型中添加一个<tt>sync</tt>方法。

默认**sync**像下面这样处理CRUD和REST映射：

*   **create → POST  ** <tt>/collection</tt>
*   **read → GET  ** <tt>/collection[/id]</tt>
*   **update → PUT  ** <tt>/collection/id</tt>
*   **patch → PATCH  ** <tt>/collection/id</tt>
*   **delete → DELETE  ** <tt>/collection/id</tt>

举个例子，<tt>Backbone</tt>可以像这样用Rails 4处理一个调用更新<tt>"update"</tt>响应：

<pre>def update
  account = Account.find params[:id]
  permitted = params.require(:account).permit(:name, :otherparam)
  account.update_attributes permitted
  render :json => account
end
</pre>

另一个建议是在整合Rails 3.1之前的版本时，通过设置<tt>ActiveRecord::Base.include_root_in_json = false</tt>， 来禁止使用模型上调用<tt>to_json</tt>时的默认命名空间。

**ajax**`Backbone.ajax = function(request) { ... };`  
如果你想去使用一个自定义AJAX函数，或者终端不支持[jQuery.ajax](http://api.jquery.com/jQuery.ajax/)API， 你可以通过设置<tt>Backbone.ajax</tt>来自定义一些事情。

**emulateHTTP**`Backbone.emulateHTTP = true`  
如果你使用一个传统的不支持Backbone默认的REST/HTTP方式的web服务器，你可以选择开启<tt>Backbone.emulateHTTP</tt>选项。 设置这个选项将模拟<tt>PUT</tt>, <tt>PATCH</tt> 和 <tt>DELETE</tt>请求为一个HTTP<tt>POST</tt>请求， 请求报文中将设置<tt>X-HTTP-Method-Override</tt>为true。 如果也开启<tt>emulateJSON</tt>选项的话，真正的方法将作为一个额外的<tt>_method</tt>参数传递。

<pre>Backbone.emulateHTTP = true;

model.save();  // POST to "/collection/id", with "_method=PUT" + header.
</pre>

**emulateJSON**`Backbone.emulateJSON = true`  
如果你使用一个传统的不支持处理请求编码为<tt>application/json</tt>的web服务器， 设置<tt>Backbone.emulateJSON = true;</tt>，将导致<tt>model</tt>的参数序列化为JSON， 请求内容类型变为<tt>application/x-www-form-urlencoded</tt>，犹如一个HTML表单请求。

## （视图）Backbone.View

Backbone视图相对它们的代码来说更多的是惯例 — 它们不决定关于HTML和CSS的任何事情，可以使用任意JavaScript模板库。 常用方法是组织你的界面为逻辑视图，支持的模型（backed by models），每个视图的更新依赖模型的更改，不需要重绘页面。 不必进入一个JSON对象，查找DOM中的一个元素，手动更新HTML，可以绑定视图渲染<tt>render</tt>方法到模型的更改<tt>"change"</tt>事件 &mdash 现在，模型的数据可以显示在UI的任何地方，它们总是最新的。

**extend**`Backbone.View.extend(properties, [classProperties])`  
从创建一个自定义视图类开始。 你可以覆盖[render](#View-render)方法，声明特殊的[events](#View-delegateEvents)事件， 和视图根元素的<tt>tagName</tt>，<tt>className</tt>, 或 <tt>id</tt>。

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

<tt>tagName</tt>, <tt>id</tt>, <tt>className</tt>, <tt>el</tt>, 和 <tt>events</tt> 等属性可以被定义为一个函数， 如果你想等到运行时再定义它们的话。

**constructor / initialize**`new View([options])`  
这有几个特殊的选项，如果传递，将直接应用于视图： <tt>model</tt>, <tt>collection</tt>, <tt>el</tt>, <tt>id</tt>, <tt>className</tt>, <tt>tagName</tt>, <tt>attributes</tt> and <tt>events</tt>。 如果视图定义了一个初始化**initialize**方法，当视图首次创建时被调用。 如果你想去通过引用已存在_already_DOM中的元素来创建一个视图，传递元素作为选项： <tt>new View({el: existingElement})</tt>

<pre>var doc = documents.first();

new DocumentRow({
  model: doc,
  id: "document-row-" + doc.id
});
</pre>

**el**`view.el`  
所有视图在任何时候都有一个DOM元素（**el**属性），无论是否已经插入到页面中去。 已这种方式，视图在任何时候都可以被渲染，一次性全部插入到DOM中去，为了获得UI渲染的高效性，尽可能减少回流（reflows）和重绘（repaints）。

<tt>this.el</tt>可以是一个DOM选择符字符串或者一个元素； 否则它会根据视图的<tt>tagName</tt>, <tt>className</tt>,<tt>id</tt> 和 [<tt>attributes</tt>](#View-attributes)属性来创建。 如果没有设置，<tt>this.el</tt>是一个空的<tt>div</tt>，这往往就够了。 一个**el**引用也可以被传递给构造函数。

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
一个包含视图元素的缓存jQuery对象。方便代替引用重新包装的DOM元素。

<pre>view.$el.show();

listView.$el.append(itemView.el);
</pre>

**setElement**`view.setElement(element)`  
如果你想去应用一个Backbone 视图到一个不同的DOM元素中，使用**setElement**， 这将创建缓存<tt>$el</tt>引用，从旧元素中移除视图的代理事件，并添加到新元素中去。

**attributes**`view.attributes`  
一个用来设置视图<tt>el</tt>包含的HTML DOM元素属性（id, class, data-properties,等等）的哈希属性，或者一个返回哈希的函数。

**$ (jQuery)**`view.$(selector)`  
如果jQuery包含在页面中，每个视图有一个**$**函数来运行视图元素范围内的查询。 如果你使用这个范围查询方法，你不需要使用模型ids作为查询参数来拉取列表中特殊的元素，可以更多依赖HTML的class属性。 它等于运行： <tt>view.$el.find(selector)</tt>。

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
While templating for a view isn't a function provided directly by Backbone, it's often a nice convention to define a **template** function on your views. 例如，使用Underscore模板： 通过这种方式，当渲染你的视图时，可以方便的访问实例数据。

<pre>var LibraryView = Backbone.View.extend({
  template: _.template(...)
});
</pre>

**render**`view.render()`  
**render**的默认实现是一个空操作（no-op），覆盖这个方法，使用模型数据来渲染视图模板， 通过新的HTML来更新<tt>this.el</tt>。 一个好的习惯是在**render**方法的结尾返回this<tt>return this</tt>，来方便链式调用。

<pre>var Bookmark = Backbone.View.extend({
  template: _.template(...),
  render: function() {
    this.$el.html(this.template(this.model.attributes));
    return this;
  }
});
</pre>

Backbone 尊重你所选择的HTML模板方法，它并不知晓。 你的**render**方法甚至可以和HTML字符串混合（munge）在一起，或者使用<tt>document.createElement</tt>来生成DOM树。 但是，我们建议选择一个漂亮的JS模板库。 [Mustache.js](http://github.com/janl/mustache.js), [Haml-js](http://github.com/creationix/haml-js), 和 [Eco](http://github.com/sstephenson/eco) 都是非常好的替代品。 因为 [Underscore.js](http://underscorejs.org/) 已存在于页面中, [_.template](http://underscorejs.org/#template) 方法可以使用, 他是一个极好的选择，如果你喜欢简单的JS插值风格的模板。

无论你最终的模板策略是什么，不在JavaScript中插入HTML字符串总是好的。 在DocumentCloud上，我们使用了[Jammit](http://documentcloud.github.com/jammit/) 来打包存在于<tt>/app/views</tt>中的JS模板，并作为我们主要资源包<tt>core.js</tt>的一部分。

**remove**`view.remove()`  
从DOM中移除一个视图和它的<tt>el</tt>，调用[stopListening](#Events-stopListening) 来移除所有绑定的视图监听[listenTo](#Events-listenTo)事件。

**events**`view.events or view.events()`  
**events**哈希（或方法）可用于指定一组DOM事件，通过[delegateEvents](#View-delegateEvents)绑定到你视图的方法。

Backbone 将在调用初始化[initialize](#View-constructor)之前，自动在实例化的时候应用事件监听器。

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
使用jQuery的<tt>on</tt>方法来注册视图中的DOM事件并声明回调。 如果没有直接传递一个事件**events**哈希，使用tt>this.events作为<事件源。 **events**的书写格式是：<tt>{"event selector": "callback"}</tt>。 回调可以是一个方法名称或者直接定义函数体。 省略<tt>selector</tt>导致事件绑定到视图的根元素（<tt>this.el</tt>）。 默认，<tt>delegateEvents</tt>在视图的构造函数中被调用，因此，如果你有一个简单的事件<tt>events</tt>哈希， 所有的DOM事件总是保持连接，你不必自己再去调用这个方法。

为使定义事件更容易，以及从父视图继承，<tt>events</tt> 属性也可以被定义为返回一个事件**events**哈希的函数。

在渲染 [render](#View-render)期间，使用**delegateEvents**比手动使用jQuery给子元素绑定事件有许多优点。 所有添加的回调绑定到视图之前被传递给jQuery。 因此，当回调被调用时，<tt>this</tt>指向的依然是视图对象。 当**delegateEvents**再次运行时，也许有一个不同的事件<tt>events</tt>哈希， 所有回调被移除，代理刷新 — 对于视图在不同的模式需要不同的行为时非常有用。

单个事件版本的 **delegateEvents**可作为<tt>delegate</tt>。 事实上，**delegateEvents**是一个简单的多事件代理<tt>delegate</tt>包装器。 一个<tt>undelegateEvents</tt>可对应一个<tt>undelegate</tt>。

一个显示搜索结果的文档视图可能看起来像这样：

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
移除视图的所有代理事件。 如果你想从临时DOM中禁止或移除一个视图，非常有用。

## 实用功能（Utility）

**Backbone.noConflict**`var backbone = Backbone.noConflict();`  
返回<tt>Backbone</tt>的原始对象，你可以使用<tt>Backbone.noConflict()</tt>的返回值来 来保持对Backbone的本地引用。对于将Backbone嵌入到第三方网站，而你又不想去覆盖现有的Backbone对象时非常有用。

<pre>var localBackbone = Backbone.noConflict();
var model = localBackbone.Model.extend(...);
</pre>

**Backbone.$**`Backbone.$ = $;`  
如果你的页面中有多个<tt>jQuery</tt>副本，或者想告诉Backbone去使用一个特殊对象作为处理DOM和Ajax的库， 可以使用这个属性。

<pre>Backbone.$ = require('jquery');
</pre>