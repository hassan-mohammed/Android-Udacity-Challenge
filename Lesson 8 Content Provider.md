# Android Best Practices || Lesson 8 Content Provider 

One of the great things about Udacity course is the development best practices that are being shared from Google development team 
So I thought it will be great idea  to collect these best practices in form of code snippets and developments tips, so one can refer to them when needed 

This post we will cover:-
* [Why to use content provider](why-to-use-content-provider)
* [General Steps for Using a ContentProvid](general-steps-for-using-a-contentprovider)


Code snippets are from SunShine App, to make it easy to relate to 

You can use these notes as a reference for you during development 





## Why to use content provider: 
Content provider is a class that sits between an app and its data source. Its job is to provide easily managed access to underlying data source.
1. Easily change underlying data source without to change any code in the apps that access the content provider
2. Classes as SyncAdapters, Loaders and CursorAdapters use content providers. If you need to use these classes you need the data accessible through a content provider
3. Allow many apps to access, use and modify a single data source securely.

## General Steps for Using a ContentProvider
1. Get permission to use the ContentProvider.

```xml
<uses-permission android:name="com.example.udacity.droidtermsexample.TERMS_READ" />
```

2. Get the ContentResolver, and Pick one of four basic actions on the data: query, insert, update, delete.
we will do this inside AsyncTask.

```java
    // Use an async task to do the data fetch off of the main thread.
    public class WordFetchTask extends AsyncTask<Void, Void, Cursor> {

        // In the doInBackground method, write the code to access the ContentProvider
        @Override
        protected Cursor doInBackground(Void... params) {
            // Make the query to get the data

            // Get the content resolver
            ContentResolver resolver = getContentResolver();

            // Call the query method on the resolver with the correct Uri from the contract class
            Cursor cursor = resolver.query(DroidTermsExampleContract.CONTENT_URI,
                    null, null, null, null);
            return cursor;
        }


        // Invoked on UI thread
        @Override
        protected void onPostExecute(Cursor cursor) {
            super.onPostExecute(cursor);
            // Set the data for MainActivity here 
        }
    }
```

3. Create and execute your AsyncTask at onCreate for example

```java
  protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

          new WordFetchTask().execute();
  }
```
