# Scheduled tasks

Darwino features a mechanism to run scheduled, or on demand, tasks on the server. A task is a Java class with an `execute()` method that is called asynchronously by the Darwino runtime.

## Defining a task
A task should inherit from the class `com.darwino.commons.tasks.Task`. It should implement the method 'execute()' to execute the task:

	public Void execute(TaskExecutorContext context) throws TaskException {
		Platform.log("Task scheduled, time:{0}",
		    DateFormatter.Standard.LONG_DATETIME.getFormat().format(new Date()));
		return null;
	}

Tasks can be used in a wider context and thus also return a value. When running schduled tasks, the return value is ignored so better to define the task as returning an object of type `Void` with the value `null`.

See: `LogTask.java`

## Scheduling a task
The Darwino platform has a background task scheduler provided as a service. Tasks can be scheduled at any time, and also removed from the scheduler.

Example:

	protected void initTasks(ServletContext servletContext, TaskProgress progress) throws JsonException {
		final TaskScheduler scheduler = Platform.getService(TaskScheduler.class);
		
		// Install the tasks
		// This tasks logs a string every 1 minute
		scheduler.scheduleTask(
				new LogTask(),
				new IntervalScheduler().interval("1m"));
	}

See: `AppContextListener.java`

