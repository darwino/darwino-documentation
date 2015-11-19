## Context
The Darwino Context provides the application with access to all of its environment information, including the user identity and the properties of the execution environment.

The Context has a different behavior depending on the platform, but it provides access to the same information.
```
DarwinoContext ctx = DarwinoContext.get();

_formatText("User: {0}",ctx.getUser());
_formatText("JsonStore Session: {0}",ctx.getSession()!=null);

```

In a web environment, there is one context object created per request. On a mobile environment, it is a singleton.

At any time, the developer can get access to the current context by calling DarwinoContext.get(). If you know the platform you're running in, and if you want to get access to platform specific features, then you can access the specific context by casting the context object to the platform specific class, or by calling get() on this class (ex: DarwinoJ2EEContext.get())