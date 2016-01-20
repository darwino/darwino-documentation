#Preferences Service

The Preferences Service is an interface for setting, reading, and removing user and application preferences.

To read a preference, create a PreferencesService object, then call getPreferences(), passing the username and preference name as arguments. Then call get(), with the preference name as the argument.

```
PreferencesService prefService = Platform.getService(PreferencesService.class);

Preferences p = prefService.getPreferences("user1","pref1");

// This default service has pref1 & pref2 defined, but not pref3
_formatText("  pref1: {0}",p.get("pref1"));
_formatText("  pref2: {0}",p.get("pref2"));
_formatText("  pref3: {0}",p.get("pref3"));
```

To set a preference value, first get the preference and then call set(), passing the preference name and value as arguments:
```
PreferencesService prefService = Platform.getService(PreferencesService.class);

// Preference is set locally in the object
Preferences p = prefService.getPreferences("user1","myprefs");
p.set("custom","value 1");
_formatText("  custom: {0}",p.get("custom"));

// Does not appear because it is not yet saved
Preferences p2 = prefService.getPreferences("user1","myprefs");
_formatText("  custom: {0}",p2.get("custom"));

// Save it, and it is now in the DB
p.save();
Preferences p3 = prefService.getPreferences("user1","myprefs");
_formatText("  custom: {0}",p3.get("custom"));


```


To delete a preference, call deletePreference(), passing the username and preference name as arguments:
```
PreferencesService prefService = Platform.getService(PreferencesService.class);

prefService.deletePreferences("user1","myprefs");

```
