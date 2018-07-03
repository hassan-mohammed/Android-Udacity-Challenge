# Android Best Practices || Lesson 7 Prefernces


One of the great things about Udacity course is the development best practices that are being shared from Google development team 
So I thought it will be great idea  to collect these best practices in form of code snippets and developments tips, so one can refer to them when needed 

This post we will cover:-
* [Adding Prefernces to your App](#adding-prefernces-to-your-app)
* [Reading From SharedPreferences](#reading-from-sharedpreferences)
* [List Prefrence](#list-prefrence)
* [Add Summary to List Preference](#add-summary-to-list-preference)


Code snippets are from SunShine App, to make it easy to relate to 

You can use these notes as a reference for you during development 

## Adding Prefernces to your App

1. Add the gradle dependency for the support preference fragment

```xml
compile 'com.android.support:preference-v7:25.1.0'
```
2. Create new Activity called activity_settings.xml and change its layout to have only fragment root

```xml
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/activity_settings"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:name="SettingsFragment"
    />
 ```

3. In the AndroidManifest.xml make these changes

```xml
 <activity
    android:name=".SettingsActivity"
    android:label="@string/action_settings"
    android:parentActivityName=".VisualizerActivity">
    <meta-data
        android:name="android.support.PARENT_ACTIVITY"
        android:value=".VisualizerActivity" />
</activity>   

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
  <CheckBoxPreference
        android:defaultValue="@bool/pref_show_bass_default"
        android:key="@string/pref_show_bass_key"
        android:summaryOff="@string/pref_show_false"
        android:summaryOn="@string/pref_show_true"
        android:title="@string/pref_show_bass_label" />
</PreferenceScreen>

```
4. Under res directory create directory called xml create a new resourse file called pref_visualizer
In pref_visualizer create a preference screen containing your settings

```xml
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
  <CheckBoxPreference
        android:defaultValue="@bool/pref_show_bass_default"
        android:key="@string/pref_show_bass_key"
        android:summaryOff="@string/pref_show_false"
        android:summaryOn="@string/pref_show_true"
        android:title="@string/pref_show_bass_label" />
</PreferenceScreen>
<!-- make sure you use values from resources files. strings from strings.xml and bool from boot.xml -- >
```

5. Create a class called SettingsFragment that extends PreferenceFragmentCompat

```java
public class SettingsFragment extends PreferenceFragmentCompat {

//Overide the onCreatePreferences method and call onCreatePreferences passing pref_visualizer that you just created 
    @Override
    public void onCreatePreferences(Bundle savedInstanceState, String rootKey) {
        addPreferencesFromResource(R.xml.pref_visualizer);
    }
}
```

6. finally under res/values/styles.xml, add a theme for the preference

```xml
<item name="preferenceTheme">@style/PreferenceThemeOverlay</item>
```

## Reading From SharedPreferences

1. Get a reference to the default shared preferences from the PreferenceManager class
```java
SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
```

2. Get the value of the preference and use it

```java
        boolean value = sharedPreferences.getBoolean("show_bass",true) //WITHOUT USING Resources NOT RECOMMENEDED 
        boolean value = sharedPreferences.getBoolean(getString(R.string.pref_show_bass_key), 
            getResources().getBoolean( R.bool.pref_show_bass_default)))
```


## List Prefrence

1. Add a list preference to the pref_visualizer.xml file
```xml
<?xml version="1.0" encoding="utf-8"?>
 <ListPreference
        android:defaultValue="@string/pref_color_red_value"
        android:key="@string/pref_color_key"
        android:title="@string/pref_color_label"
        android:entries="@array/pref_color_option_labels"
        android:entryValues="@array/pref_color_option_values"
        />
 ```
2. Add string values for the list preference
```xml
<?xml version="1.0" encoding="utf-8"?>
    <string name="pref_color_red_value" translatable="false">red</string>
    <string name="pref_color_blue_value" translatable="false">blue</string>
    <string name="pref_color_green_value" translatable="false">green</string>
 ```
3. Add a res->values->arrays.xml file which contains two arrays, one for labels and one for values.
```xml
<?xml version="1.0" encoding="utf-8"?>
 <array name="pref_color_option_labels">
        <item>@string/pref_color_label_red</item>
        <item>@string/pref_color_label_blue</item>
        <item>@string/pref_color_label_green</item>

    </array>

    <array name="pref_color_option_values">
        <item>@string/pref_color_red_value</item>
        <item>@string/pref_color_blue_value</item>
        <item>@string/pref_color_green_value</item>
    </array>

<!-- Note: the ordering of the labels has to match the ordering of the values -->

4. Update setupSharedPreferences and 
onSharedPreferenceChanged to load the color

```xml
mVisualizerView.setColor(sharedPreferences.getString(getString(R.string.pref_color_key), getString(R.string.pref_color_red_value)));
```

## Add Summary to List Preference

1. Implement OnSharedPreferenceChangeListener

```java
public class SettingsFragment extends PreferenceFragmentCompat implements
        OnSharedPreferenceChangeListener {

    @Override
    public void onCreatePreferences(Bundle bundle, String s) {

        // [1.1] Get the preference screen, get the number of preferences and iterate through
        // all of the preferences if it is not a checkbox preference, call the setSummary method
        // passing in a preference and the value of the preference

        SharedPreferences sharedPreferences = getPreferenceScreen().getSharedPreferences();
        PreferenceScreen prefScreen = getPreferenceScreen();
        int count = prefScreen.getPreferenceCount();

        for (int i = 0; i < count; i++) {
            Preference p = prefScreen.getPreference(i);
            if (!(p instanceof CheckBoxPreference)) {
                String value = sharedPreferences.getString(p.getKey(), "");
                setPreferenceSummary(p, value);
            }
        }
    }
```

2. Create a setPreferenceSummary which takes a Preference and String value as parameters.
```java
    // This method should check if the preference is a ListPreference and, if so, find the label
    // associated with the value. You can do this by using the findIndexOfValue and getEntries methods
    // of Preference.
   
    private void setPreferenceSummary(Preference preference, String value) {
        if (preference instanceof ListPreference) {
            // For list preferences, figure out the label of the selected value
            ListPreference listPreference = (ListPreference) preference;
            int prefIndex = listPreference.findIndexOfValue(value);
            if (prefIndex >= 0) {
                // Set the summary to that label
                listPreference.setSummary(listPreference.getEntries()[prefIndex]);
            }
        }

 ```
3. Override onSharedPreferenceChanged and, call setPreferenceSummary on the changed preference
 ```java   
    @Override
    public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
        Preference preference = findPreference(key);
        if (null != preference) {
            if (!(preference instanceof CheckBoxPreference)) {
                String value = sharedPreferences.getString(preference.getKey(), "");
                setPreferenceSummary(preference, value);
            }
        }
    }
```

4. Register and unregister the OnSharedPreferenceChange listener 

    // in onCreate and onDestroy respectively, as in the previous section 

  
    }





