JavaScript APIs
=======================
# 2	Generic APIs
Darwino uses the namespace “Darwino”. Everything that belongs to Darwino is within the Darwino object. This cannot be changed.

Included in the darwino.js is Darwino.jstore, which is an entire JSON store API. This is the entry point when you want to use the JavaScript wrappers for the JSON data store. 

You can directly call the JavaScript services, or you can use these wrappers, or you can use both. It’s a matter of convenience. 

From darwino.jstore, you can call createRemoteApplication(), passing it a url for where the Darwino runtime is running (for example: “$darwino-jstore”). This returns a pointer to the remote server. From that, you can call createSession(). With no parameters, it creates a session for the anonymous user, or, if logged in, for the current authenticated user. Passed a username and password, it will create a session on behalf of the specified user. Every operation performed from that session will use the rights and identity of the session’s user.

Once you have the session object, you have the exact same API capabilities that you have in Java. There are, however, several details that are specific to JavaScript:
-	The system constants (for example “SYSTEM_READERS” and “SYSTEM_WRITERS”) that are defined in Java are also defined in JavaScript. They are accessible through standard dot notation, as in:
Darwino.jstore.Database.STORE_COMMENTS

- JSONPath is implemented in JavaScript as it is in Java.

- Another point specific for JavaScript is the way we handle binary content. Because JavaScript is restricted to manipulating the binary data as Base64, it is more efficient to do such work on the server in Java via REST services and just display the value, or values, or links inside the HTML. JavaScript is not designed for this.
- Synchronous vs. Asynchronous calls
To create an application that is responsive and not often blocking the user you have to use asynchronous JavaScript, which means that when you call a service you’re not blocking the UI thread. The entire JavaScript Darwino API allows you to do asynchronous calls. You may choose to do either synchronous or asynchronous calls, but synchronous calls should be used only when the application demands them. Asynchronous is the default; if you want to do synchronous calls, you have to pass parameters, either at the session to change the default (session.Async(false);) or with each individual call.

 By default, when you call a JavaScript function that triggers a call to a service, what it returns is a promise. The latest generation of browsers supports promises, but because not all browsers do Darwino provides an A+ Compliant version that is backwards-compatible with older browsers. 

 A promise is a call that will eventually be executed. For example, session.getDatabase() will return a promise. The promise itself has a “then” method which takes as its parameters a function to execute upon successful completion of the promise, and a function to execute in the case of failure.
 
 Promises can be chained.

 Some functions, such as getDatabase(), will return a promise, while others, such as getStore() will return a real value. There is no way to differentiate between the two type other than the fact that if a function has a parameter called “header” then it is an asynchronous function and will return a promise. 

 The [Darwino Playground](http://playground.darwino.com/playground.nsf/JavaScriptSnippets.xsp#snippet=/Json%20Store/Async) is a resource for examples of synchronous and asynchronous calls and promise handling.

```
 var s = "";

session.getDatabase("playground",null,function(database) {
  var store = database.getStore("pinball");
  
  // The document is loaded asynchronously
  store.loadDocument("1000", null, function(doc) {
      s += ">> Document\n"
      s += "  Unid: "+doc.getUnid()+"\n"
      s += "  Id: "+doc.getDocId()+"\n"
      s += "  Json: "+doc.getJsonString()+"\n"
      darwino.Utils.setText("content","{0}",s);
  });
  
  // This document does not exist
  // So the function is only called in case of success
  // Nothing happens in case of an error
  store.loadDocument("1000FAKE", null, function(doc) {
      s += "!!! Should never be displayed as the document does not exist\n"
      darwino.Utils.setText("content","{0}",s);
  });
    
});

s += "Loading document...\n"

darwino.Utils.setText("content","{0}",s);
```
