## Context
The Darwino Context provides the application with access to all of its environment information, including the user identity and the properties of the execution environment.

The Context has a different behavior depending on the platform, but it provides access to the same information.
```
DarwinoContext ctx = DarwinoContext.get();

_formatText("User: {0}",ctx.getUser());
_formatText("JsonStore Session: {0}",ctx.getSession()!=null);

```