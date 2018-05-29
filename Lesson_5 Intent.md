# Android Best Practices || Lesson 5 Intent 

One of the great things about Udacity course is the development best practices that are being shared from Google development team 
So I thought it will be great idea  to collect these best practices in form of code snippets and developments tips, so one can refer to them when needed 

This post we will cover:-
* [ Explict Intent](#explict-intent)
* [Implicit Intent](#implicit-intent)

Code snippets are from SunShine App, to make it easy to relate to 

You can use these notes as a reference for you during development 


 ## Explict Intent


### Start child Activity using intent 

```java
{   
    Context context = this;
    Class destinationClass = DetailActivity.class;
    Intent intentToStartDetailActivity = new Intent(context, destinationClass);
    //Instead of Intent.EXTRA_TEXT you can use any constant string as the key for your message 
    intentToStartDetailActivity.putExtra(Intent.EXTRA_TEXT, weatherForDay);
    startActivity(intentToStartDetailActivity);
}
```


### Read intent content at the detailed activity  

```java
{
    Intent intentThatStartedThisActivity = getIntent();
    if (intentThatStartedThisActivity.hasExtra(Intent.EXTRA_TEXT)) 
        String textEntered = intentThatStartedThisActivity.getStringExtra(Intent.EXTRA_TEXT);
}
```

<br/>

 ## Implicit Intent


###  Open web page on the internet
```java
    private void openWebPage(String url) {
        Uri webpage = Uri.parse(url);
        // Here, we create the Intent with the action of ACTION_VIEW. This action allows the user
         // to view particular content. In this case, our webpage URL.
        Intent intent = new Intent(Intent.ACTION_VIEW, webpage);
        if (intent.resolveActivity(getPackageManager()) != null) {
            startActivity(intent);
        }
    }
```


### Open location on the map 
   
```java
   // This method will fire off an implicit Intent to view a location on a map.
    private void showMap(Uri geoLocation) {
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setData(geoLocation);
        if (intent.resolveActivity(getPackageManager()) != null) {
            startActivity(intent);
        }
    }
```

### Share text intent 

```java
//This method shares text and allows the user to select which app they would like to use to
     //share the text. 
    private void shareText(String textToShare) {
        
        String mimeType = "text/plain";
        String title = "Learning How to Share";

        // Use ShareCompat.IntentBuilder to build the Intent and start the chooser
        ShareCompat.IntentBuilder
                /* The from method specifies the Context from which this share is coming from */
                .from(this)
                .setType(mimeType)
                .setChooserTitle(title)
                .setText(textToShare)
                .startChooser();
    }
```
