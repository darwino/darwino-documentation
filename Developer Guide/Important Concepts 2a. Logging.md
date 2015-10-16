## Logging
Darwino provides its own logging library for each platform, and dynamically chooses the appropriate one for current platform. This make it completely transparent to the developer; your logging code will be fully cross-platform.
```
// General logging
Platform.log("Logging {0} unconditionally", "My Message");
Platform.log(new Exception(),"Logging an Exception, {0}", "Here it is");

```
Platfom.log is the basis, but it can also use the more powerful concept of log groups.