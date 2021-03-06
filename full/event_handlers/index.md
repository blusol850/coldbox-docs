# Event Handlers

Event handlers are ColdBox's version of **controllers** in the MVC design pattern. So every time you hear "event handler," you are talking about a controller that can listen to external events or internal events in ColdBox.

<img src="/images/ControllerLayer.jpg">

## CFCs
Event handlers are implemented as CFCs that are responsible for handling requests coming into the application from either a local source (like form, URL or REST) or a remote source (like Flex, Air or SOAP). These event handlers carry the task of controlling your application flow, calling business logic, preparing a display to a user and more.

> **Info:** Event Handlers are treated as **singletons** by ColdBox, so make sure you make them thread-safe and properly scoped. Persistence is controlled by the <code>coldbox.handlerCaching</code> directive

A simple event handler:
```js
component extends="coldbox.system.EventHandler"{
    function index( event, rc, prc ){}
}
```

> **Info** You can also remove the inheritance from the CFC (preferred method) and WireBox will extend the <code>coldbox.system.EventHandler</code> for you using [Virtual Inheritance](https://wirebox.ortusbooks.com/content/virtual_inheritance/).

## Constructors
Event handler controllers do not require a constructor because the extended class already provides one.  However, if you want one, you can still create one:

**Non-inheritance**
```js
component{

	function init(){
		// my stuff here
		return this;
	}

}
```

> **Info** You have access to a <code>$super</code> object in this approach.

**Inheritance**

```js
component extends="coldbox.system.EventHandler"{

	function init( required controller ){
		// init super
		super.init( arguments.controller );
		// my stuff here
		return this;
	}

}
```

## Handler Actions
Every method in this event handler controller that has an access of `public` is automatically exposed as a runnable event in ColdBox and will be auto-registered for you. That means there is no extra configuration or XML logic to define them. By convention they become alive once you create them and clients can request them. In ColdBox terms, each of these event handler methods are referred to as **actions**. ColdBox captures an incoming variable called `event` (from the form, URL or remote scopes) and uses it to execute the correct event handler and action method.

```js
component extends="coldbox.system.EventHandler"{

	function index( event, rc, prc ){
		return "Hi from handler land!";
	}

	private function myData( event, rc, prc ){
		return ['coldbox', 'wirebox', 'cachebox', 'logbox'];
	}
}
```

So what about <code>private</code> functions?  You can still use them in your application as local helpers to a handler object or can be called across handlers via a nice function called <code>runEvent()</code> that we will explore later.


### Default Action: index()
The default action of ANY handlers is the method `index()`.  If you try to execute an event handler without defining an action, ColdBox will execute the default action instead.

> **Danger** : Event Handlers are not to be used to write business logic.  They should be light and fluffy!


### Action Arguments

```js
function myAction( event, rc, prc ){
	return "Hi from handler land!";
}
```

Each method action that you write receives some arguments:

* **event** : The request context object reference (<code>coldbox.system.web.context.RequestContext</code>)
* **rc** : A reference to the request collection inside of the request context object
* **prc** : A reference to the private request collection inside of the request context object

> **Note** The **rc** and **prc** references each method receives are sent for convenience so that you can interact with the structures instead of through the **event** object's methods. Interacting with structures over methods is much more performant.

The request context object has tons of methods to help you in setting and getting variables from one layer to another, getting request metadata, rendering RESTful content, setting HTTP headers, and more. It is your information superhighway for specific requests. Remember that the API Docs are your best friend!
