# Utility Libraries

Darwino comes with a set of general utility classes. These classes are located in a variety of projects. A few of the most noteworthy are described here.

## StringUtil
In dwo-commons is StringUtil. It consists of routines to simplify handling Strings.

For example, throughout Darwino a null string and an empty value are considered as equivalent. This is similar to how JavaScript handles the two. For convenience and simplicity, Darwino brings this approach to Java via the isEmpty() and isNotEmpty() functions in StringUtil.

## AbstractException and AbstractRuntimeException 
All of the Java exceptions that are thrown by Darwino inherit directly or indirectly from these classes. Darwino’s exception classes provide additional features on top of the classes provided by Java, in particular for debugging.  They implement and enforce an exception-chaining pattern; every time an exception is caught in Darwino and another exception is thrown, the original exception is passed as a parameter to the new exception.

```
public AbstractException(Throwable nextException) {
	this(nextException, nextException==null?””:nextException.getMessage() );
}
```

To enforce this behavior, all of the constructors of Darwino exceptions must have this parameter. It may be null, but it must be present; a conscious decision is required to omit the passed exception.

A clear benefit of this exception passing is that stack traces are more informative than they would be otherwise.

## Messages
The Messages class provides a simple mechanism for accumulating information about errors and warnings. A Message consists of a severity int and a message String. The Messages that are accumulated can then be handled as a group, perhaps for presentation to the user.

## Profiler
There is a profiler bundled with Darwino. There is no UI provided, but beyond the Java interface there is a REST service that can provide access to the profiler data. This is an application profiler, as opposed to a low-level profiler. It provides the ability to add hooks into application code to monitor high-level routines and then to dump that collected information later. 

## HttpClient
The HttpClient service is an easy-to-use implementation that is JSON-friendly. Methods such as getAsJson() (which parses the value and returns it as a JSON object), putAsJson(), deleteAsJson(), and postAsJson() simplify working with REST services. Because it works the same on all Darwino platforms, code implementing it doesn’t have to be concerned with platform-specific differences.

## Tasks
A task is a piece of code that can be executed synchronously or asynchronously. Darwino’s task framework encapsulates the standard task execution implementation of each supported platform, allowing application code to remain unconcerned with the particulars of each platform.

When code executes a Task, it has access to the TaskExecutorService. Which enables passing parameters to tasks. Thus, tasks can run with different contexts. When the tasks’s execute() method is called, it is passed a TaskExecutorContext. This context contains the parameters.

If the platform’s task executor maintains progress information about its tasks, the Darwino’s TaskExecutorService can provide that progress information to the context. Darwino provides progress dialogs for the various platforms.

The TaskExecutorContext includes an updateUi() method with a Runnable that allows the backend task to update the user interface as needed. The UI task is then executed in the UI thread, which is required on client apps.

There is also a task scheduler. It allows one-time executions and scheduling by periodic intervals, and it supports time ranges (for example, “run hourly between 7:00am and 5:00pm”).

#3 Tracer
The HttpTracerService can trace all of the requests that are coming to the server. As long as the requests are being served by the HttpService, the tracer (a managed bean) can be told precisely what should be traced. Tracing can be restricted to specific urls and particular types of data (such as headers, details, and content).
