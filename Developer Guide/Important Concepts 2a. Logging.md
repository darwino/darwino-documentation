## Logging
Darwino provides its own logging library for each platform, and dynamically chooses the appropriate one for current platform. This make it completely transparent to the developer; your logging code will be fully cross-platform.
```
// General logging
Platform.log("Logging {0} unconditionally", "My Message");
Platform.log(new Exception(),"Logging an Exception, {0}", "Here it is");

```

The log() method will print unconditionally to the console when running on the web application server, or to the Android or iOS logging mechanism when running on the mobile device.

###Log groups
While Platfom.log is the basis for logging in Darwino, we can also use the more powerful concept of log groups. Log groups in Darwino are an abstraction of the various platforms' built-in logging mechanisms.

A group is a named object, typically with a hierarchical name of the form "a.b.c". You can log different classes of messages, such as information, warnings, errors, and debug information to a group. Then you can enable a particular a logging level for a specific group. 

For example, you could create a log group named "archive". You could then refer to "archive.info" and pass some strings or content. You could enable this group for a particular level of logging, for example warnings, or errors plus warnings, or errors plus warnings plus information, or everything including debug information.

Generally, you define hierarchical groups; this creates the possibility of functional logging. You could then enable logging only, for example, for "archive.permanent" or "archive.permanent.*".