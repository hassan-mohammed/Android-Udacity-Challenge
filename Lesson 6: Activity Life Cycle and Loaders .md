# Android Best Practices || Lesson 6 Activity Life Cycle and Loaders  

One of the great things about Udacity course is the development best practices that are being shared from Google development team 
So I thought it will be great idea  to collect these best practices in form of code snippets and developments tips, so one can refer to them when needed 

This post we will cover:-
* [ Android Activity Life Cycle](#android-activity-life-cycle)
* [Creating and Using AsyncTaskLoader](#creating-and-using-asynctaskloader)

Code snippets are from SunShine App, to make it easy to relate to 

You can use these notes as a reference for you during development 




## Android Activity Life Cycle

![Activity Life Cycle](https://raw.githubusercontent.com/hassan-mohammed/Android-Udacity-Challenge/master/images/Activity%20Life%20Cycle%20-%20Copy.jpg)

### Save and Restore Data Using OnSaveInstanceState

_The method onSaveInstanceState(Bundle) is called before placing the activity in such a background state, allowing you to save away any dynamic instance state in your activity into the given Bundle, to be later received in onCreate(Bundle) if the activity needs to be re-created._
_//Save status by Override onSaveInstanceState_ 

1. Create a key String for saving the data 

 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;`   private static final String LIFECYCLE_CALLBACKS_TEXT_KEY = "callbacks";`

2. Override onSaveInstanceState
```java
    @Override
    protected void onSaveInstanceState(Bundle outState) {
        // Call super.onSaveInstanceState
        super.onSaveInstanceState(outState);
        // Put the string or anything that you want to save 
        outState.putString(LIFECYCLE_CALLBACKS_TEXT_KEY, "lifecycleDisplayTextViewContents");
    }
```

3. Get saved Status at onCreate 
```java
    protected void onCreate(Bundle savedInstanceState) {
        //...
        //If savedInstanceState is not null and contains LIFECYCLE_CALLBACKS_TEXT_KEY
        if(savedInstanceState !=null){
            if(savedInstanceState.containsKey(LIFECYCLE_CALLBACKS_TEXT_KEY)){
                String previousStateString = savedInstanceState.getString(LIFECYCLE_CALLBACKS_TEXT_KEY);
            }
        }
    }
```

<br/>

## Creating and Using AsyncTaskLoader



Loaders provide a framework to perform asynchronous loading of data. 
They're registered by ID, with a component called the LoaderManager, **which allows them to live beyond the life cycle of the activity they are associated with**, preventing duplicate loads from happening in parallel. 


1. We start off by creating an integer constant for a loader ID.

 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;`     private static final int FORECAST_LOADER_ID = 3;`
    

2. Then, we implement the loader call backs interface. 

```java
public class MainActivity extends AppCompatActivity implements LoaderCallbacks<String[]> {

    // [2.2] Override onCreateLoader, and return a new AsyncTaskLoader .
    @Override
    public Loader<String[]> onCreateLoader(int id, final Bundle loaderArgs) {

        return new AsyncTaskLoader<String[]>(this) {

            /* This String array will hold and help cache our weather data */
            String[] mWeatherData = null;

            //  Cache the weather data in a member variable and deliver it in onStartLoading.
            @Override
            protected void onStartLoading() {
                if (mWeatherData != null) {
                    deliverResult(mWeatherData);
                } else {
                    mLoadingIndicator.setVisibility(View.VISIBLE);
                    forceLoad();
                }
            }

            //loadInBackground will load and parse the JSON data in the background. 
            @Override
            public String[] loadInBackground() {
                //do your heavy work here 
            }

            // Sends the result of the load to the registered listener.
            public void deliverResult(String[] data) {
                mWeatherData = data;
                super.deliverResult(data);
            }
        };
    }


     // [2.3] Override onLoadFinished
    //Called when a previously created loader has finished its load.
    @Override
    public void onLoadFinished(Loader<String[]> loader, String[] data) {
        //show the data that result from loader task 
        if (null == data) showErrorMessage();
        else  showWeatherDataView()
    }

    //[2.4]Override onLoaderReset, usually you don't do anything in this method
    @Override
    public void onLoaderReset(Loader<String> loader) {

    }

}  
```

3. Finally, we initialize the loader with the loaderManage at onCreate method
```java
    protected void onCreate(Bundle savedInstanceState) {
        //...
        Bundle bundleForLoader = null;
        getSupportLoaderManager().initLoader(FORECAST_LOADER_ID, bundleForLoader, this);

        //
       
        /* The second parameter of the initLoader method below is a Bundle. Optionally, you can
        * pass a Bundle to initLoader that you can then access from within the onCreateLoader
        * callback. */
        Bundle queryBundle = new Bundle();
        queryBundle.putString(SEARCH_QUERY_URL_EXTRA, githubQuery);
    }
```
