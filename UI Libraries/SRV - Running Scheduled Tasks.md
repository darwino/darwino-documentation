# Scheduled tasks

Darwino features a mechanism to run scheduled, or on demand, tasks on the server. A task is a Java class with an `execute()` method that is called asynchronously by the Darwino runtime.

## Defining a task
A task should inherit from the class `com.darwino.commons.tasks.Task`. It should implement the method 'execute()' as bellow:

	public Void execute(TaskExecutorContext context) throws TaskException {
		Platform.log("Task scheduled, time:{0}",
		    DateFormatter.Standard.LONG_DATETIME.getFormat().format(new Date()));
		return null;
	}

Tasks can be used in a wider context and can return a value. When running scheduled tasks, the return value is ignored so better to define the task as returning an object of type `Void` with the returned value being `null`.

See: `LogTask.java`

## Scheduling a task
The Darwino platform has a background task scheduler provided as a service. Tasks can be scheduled at any time, and also removed from the scheduler.

Example:

    protected void initTasks(ServletContext servletContext, 
        TaskProgress progress) throws JsonException {
	final TaskScheduler scheduler = Platform.getService(TaskScheduler.class);
	// Install the tasks
	// This tasks logs a string every 1 minute
	scheduler.scheduleTask(
		new LogTask(),
		new IntervalScheduler().interval("1m"));
    }

As a task executes in the background, and not from an HTTP request, it does not have a DarwinoContext object available. Thus, if the task needs to access the database, the task should create a temporary session with using a predefined user object, or as a system admin. The session should be closed when the task is executed, to not hold any physical connection to the database.

Example:

    public Void execute(TaskExecutorContext context) throws TaskException {
        LocalJsonDBServer srv = DarwinoApplication.get().getLocalJsonDBServer();
	Session session = srv.createSystemSession(null);
	try {
	    ...
	} finally {
	    StreamUtil.close(session);
	}
	return null;
    }


See: `AppContextListener.java`

