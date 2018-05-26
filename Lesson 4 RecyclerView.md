# Android Best Practices || Lesson 4 RecyclerView

One of the great things about Udacity course is the development best practices that are being shared from Google development team 
So I thought it will be great idea  to collect these best practices in form of code snippets and developments tips, so one can refer to them when needed 

This post we will cover:-
* [Creating RecyclerView](#steps-for-creating-and-using-recyclerview)
* [Responding to Item Clicks](#recyclerview-responding-to-item-clicks)

Code snippets are from SunShine App, to make it easy to relate to 

You can use these notes as a reference for you during development 


## Steps for Creating and Using RecyclerView


1. Add RecyclerView dependency
```java
    compile 'com.android.support:recyclerview-v7:25.0.1'
```


2. Add RecyclerView to your layout 

```xml
     <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerview_forecast"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
```
3. Add a layout for an item in the list  
```java
//This should be a new xml layout with root element like LinearLayout 
```

 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;  _Example_ [forecast_list_item.xml](https://github.com/udacity/ud851-Sunshine/blob/S03.01-Solution-RecyclerView/app/src/main/res/layout/forecast_list_item.xml)


4. Create the Adapter class (which  will connect our data to our RecyclerView and determine the ViewHolder which will need to be used to display that data.)


```java
     //[4.1] Extend RecyclerView.Adapter<ForecastAdapter.ForecastAdapterViewHolder>
    public class ForecastAdapter extends RecyclerView.Adapter<ForecastAdapter.ForecastAdapterViewHolder> {

    //Create the default constructor 
    public ForecastAdapter() {

    }

    // [4.2] Create the ViewHolder class that Extend RecyclerView.ViewHolder
    //This ViewHolder will Cache of the children views for your recyclerview
    public class ForecastAdapterViewHolder extends RecyclerView.ViewHolder {

       //[4.2.1] Create variables for each view in your item layout 
        public final TextView mWeatherTextView;

        // [4.2.2] Create a constructor for this class that accepts a View as a parameter
        public ForecastAdapterViewHolder(View view) {
            //[4.2.3]Call super(view)
            super(view);
            //[4.2.4]get a reference to all your views in the item layout 
            mWeatherTextView = (TextView) view.findViewById(R.id.tv_weather_data);
        }
    }

    
    //[4.3]  Override onCreateViewHolder This gets called when each new ViewHolder is created.
    @Override
    public ForecastAdapterViewHolder onCreateViewHolder(ViewGroup viewGroup, int viewType) {
        // inflate the list item xml into a view, and return a new ForecastAdapterViewHolder
        Context context = viewGroup.getContext();
        int layoutIdForListItem = R.layout.forecast_list_item;
        LayoutInflater inflater = LayoutInflater.from(context);
        boolean shouldAttachToParentImmediately = false;

        View view = inflater.inflate(layoutIdForListItem, viewGroup, shouldAttachToParentImmediately);
        return new ForecastAdapterViewHolder(view);
    }

    
    // [4.4] Override onBindViewHolder, called by the RecyclerView to display the data at the specified position
    @Override
    public void onBindViewHolder(ForecastAdapterViewHolder forecastAdapterViewHolder, int position) {
        //Set your views with data for this list item's position, as the below example 
        String weatherForThisDay = mWeatherData[position];
        forecastAdapterViewHolder.mWeatherTextView.setText(weatherForThisDay);
    }

    //[4.5] Override getItemCount, to returns the number of items to display
    @Override
    public int getItemCount() {
        if (null == mWeatherData) return 0;
        return mWeatherData.length;
    }

}

```

5. Using the RecyclerView 

```java
    //At the activity where you will use the recyclerview
    //[5.1] Add a private RecyclerView, and Adapter variables 
    private TextView mErrorMessageDisplay;
    private ProgressBar mLoadingIndicator;
    
    //at onCreate Method 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        //..........
        
        //[5.2]Intialize RecyclerView and Adapter objects 
        mRecyclerView = (RecyclerView) findViewById(R.id.recyclerview_forecast);
        mForecastAdapter = new ForecastAdapter();

        // [5.3] Create your layoutManager, and set it on recyclerview  
        LinearLayoutManager layoutManager
                = new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false);
        mRecyclerView.setLayoutManager(layoutManager);

        //you may  Use setHasFixedSize(true)to designate that all items will have the same size
        mRecyclerView.setHasFixedSize(true);

        //[5.4] Use mRecyclerView.setAdapter and pass in mForecastAdapter
        mRecyclerView.setAdapter(mForecastAdapter);
    }


```

<br/>


## RecyclerView Responding to Item Clicks


1. Inside your Adapter class, add an interface and define an onClick method inside it 

```java        
public interface ForecastAdapterOnClickHandler {
            void onClick(String weatherForDay);
        }

```

2. Also create variable from the interface and initialize it in the constructor using the one passed from the calling activity   

```java
private final ForecastAdapterOnClickHandler mClickHandler;
    public ForecastAdapter(ForecastAdapterOnClickHandler clickHandler) {
        mClickHandler = clickHandler;
    }

```


3. Implement OnClickListener in the ViewHolder class

```java
    public class ForecastAdapterViewHolder extends RecyclerView.ViewHolder implements OnClickListener {

        //inside constructor Call setOnClickListener on the view passed into the constructor (use 'this' as the OnClickListener)
        public ForecastAdapterViewHolder(View view) {
            //......
            view.setOnClickListener(this);
        }

       // Override onClick, This gets called by the child views during a click.        @Override
        public void onClick(View v) {
            int adapterPosition = getAdapterPosition();
            String weatherForDay = mWeatherData[adapterPosition];
            mClickHandler.onClick(weatherForDay);
        }
    }

```
4. Implement ForecastAdapterOnClickHandler from the MainActivity and Override onClick method


```java
public class MainActivity extends AppCompatActivity implements ForecastAdapterOnClickHandler {
//....
    
    private ForecastAdapter mForecastAdapter;
   
    @Override
    protected void onCreate(Bundle savedInstanceState) {
    //... 
    //Pass in 'this' as the ForecastAdapterOnClickHandler
        mForecastAdapter = new ForecastAdapter(this);
    }

     @Override
     public void onClick(String weatherForDay) {
              //handle the click 
    }


}

```

<br/>
