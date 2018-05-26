# Android Best Practices || Lesson 1-3 Menus, AsyncTask, Toast

One of the great things about Udacity course is the development best practices that are being shared from Google development team 
So I thought it will be great idea  to collect these best practices in form of code snippets and developments tips, so one can refer to them when needed 

This post we will cover:- 
* [Creating and Using Menus](#steps-for-creating-menus)
* [AsyncTask](#steps-for-using-asynctask)
* [Creating and Using Toast](#creating-and-using-toast)

Code snippets are from SunShine App, to make it easy to relate to 

You can use these notes as a reference for you during development 


## Steps for creating Menus


1. Create  an xml layout for the menu
```xml 
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">   
   // <!--Show this item if there is room (use app:showAsAction, not android:showAsAction)-->
   <item
        android:id="@+id/action_search"
        android:orderInCategory="1"
        app:showAsAction="ifRoom"
        android:title="@string/search"/>
</menu>
```

2. Inside the displaying activity,  we need to override onCreateOptionsMenu() to inflate the menu

```java
 @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        /* Use AppCompatActivity's method getMenuInflater to get a handle on the menu inflater */
        MenuInflater inflater = getMenuInflater();
        /* Use the inflater's inflate method to inflate our menu layout to this menu */
        inflater.inflate(R.menu.forecast, menu);
        /* Return true so that the menu is displayed in the Toolbar */
        return true;
    }
```
 
3. Then to handle menu item selections, we override onOptionsItemSelected 

 ```java
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();

        if (id == R.id.action_refresh) {
            mForecastAdapter.setWeatherData(null);
            loadWeatherData();
            return true;
        }

        return super.onOptionsItemSelected(item);
    }
```




## Steps for Using AsyncTask


```java
//[1] **Create a class that extends AsyncTask with <Params, Progress, Result>**
    public class FetchWeatherTask extends AsyncTask<String, Void, String[]> {

      // [2] Override the doInBackground method to perform your long running task 
        @Override
        protected String[] doInBackground(String... params) {
            //[Invoked on the background thread ]
        }

       //[3] Override the onPostExecute method to display or return results 
        @Override
        protected void onPostExecute(String[] weatherData) {
            //[invoked on the UI thread after the background computation finishes]
        }

        //You may need to override onPreExecute to do some initialization 
        @Override
        protected void onPreExecute() {
            // [invoked on the UI thread before the task is executed]
        }

        //You may need to override onProgressUpdate to update UI with progress  
        @Override
        protected void onProgressUpdate(Void... values) {
          //[invoked on the UI thread]
        }

    }

```






## JSON Parsing



Consider this JSON string 

```java
    {
       "temp": {
          "min":"11.34",
          "max":"19.01"
       },
       "weather": {
          "id":"801",
          "condition":"Clouds",    //need to get this value 
          "description":"few clouds"
       },
       "pressure":"1023.51",
       "humidity":"87"
    }

```

This function get the condition value 

```java

    String getCondition(String JSONString) {

       JSONObject forecast = new JSONObject(JSONString);
       JSONObject weather = forecast.getJSONObject("weather");
       return weather.getString("condition");
    }

```




## Creating and Using Toast


                   

```java
                                             //Duration of display
  Toast toast = Toast.makeText(context, text, Toast.LENGTH_SHORT);
      //Show a toast 
    toast.show();
```
