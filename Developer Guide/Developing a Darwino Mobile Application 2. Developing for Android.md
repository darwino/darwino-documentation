# 	Developing for Android
When creating a hybrid app for Android, the wizard generate the classes required by the Android SDK, including AndroidApplication.java, AndroidHybridActions.java, and SplashScreenActivity.java. It also generates the same classes used by the J2EE applications. One exception of interest is the DarwinoServiceDispatcher, which in this case inherits from DarwinoHttpServer.

The wizard’s output for a native app is smaller; there is no HTTP server included. The wizard will generate the DarwinoApplication class and the MainActivity, but it will leave creating the UI to the developer.
