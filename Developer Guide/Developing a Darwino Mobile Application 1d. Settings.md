## Settings
exec() is a way for the web side of the app to communicate with native device code. The converse of that is done via registered listeners. For example, the settings listener will notify the app when something has changed in the device settings. The app could then use isDirty() to see if the settings have changed since the last refresh, and then read and set settings as required. Several functions are provided to assist in this:
- addSettingsListener()
- setSettings()
- getProperty()
- getMode()
- isDirty()
- setDirty()
