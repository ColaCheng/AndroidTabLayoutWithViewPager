# Android TabLayout with ViewPager

We will introduce Google's new [TabLayout](https://developer.android.com/reference/android/support/design/widget/TabLayout.html) included in the support design library release for Android "M".

### Add Support Library

To implement sliding tabs, make sure to add the Support Library setup instructions first. (make sure these versions have been updated.)

 ```gradle
dependencies {
  compile 'com.android.support:appcompat-v7:22.2.0'
  compile 'com.android.support:design:22.2.0'
}
 ```
  
### Sliding Tabs Layout

Add `android.support.design.widget.TabLayout` that will handle the different tab options, and `android.support.v4.view.ViewPager` component will handle the page between the various fragments we will create.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <android.support.design.widget.TabLayout
        android:id="@+id/tab_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>
    <!--app:tabMode="scrollable"-->

    <android.support.v4.view.ViewPager
        android:id="@+id/pager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</LinearLayout>
```

### Create Fragment

We define the XML layout(`res/layout/fragment_content.xml`) for the fragment which will be displayed on screen when a particular tab is selected:
```xml
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.cola.tablayoutwithviewpager.ContentFragment">

    <!-- TODO: Update blank fragment layout -->
    <TextView
        android:id="@+id/title_txtView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:textSize="20sp"
        android:gravity="center"
        android:text="@string/hello_blank_fragment" />

</FrameLayout>
```


Then `ContentFragment.java` define the logic for the fragment of tab content:

```java
public class ContentFragment extends Fragment {
    private static final String ARG_PARAM1 = "param1";

    // TODO: Rename and change types of parameters
    private String mParam1;

    private TextView titleTxtView;

    public static ContentFragment newInstance(String param1) {
        ContentFragment fragment = new ContentFragment();
        Bundle args = new Bundle();
        args.putString(ARG_PARAM1, param1);
        fragment.setArguments(args);
        return fragment;
    }

    public ContentFragment() {
        // Required empty public constructor
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (getArguments() != null) {
            mParam1 = getArguments().getString(ARG_PARAM1);
        }
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        View myView = inflater.inflate(R.layout.fragment_content, container, false);
        titleTxtView = (TextView)myView.findViewById(R.id.title_txtView);
        titleTxtView.setText(mParam1);
        return myView;
    }
    
}
```

### Implement FragmentPagerAdapter

Following, we implement the adapter for your `ViewPager` which controls the order of the tabs, the titles and their associated content. The most important methods to implement here are `getPageTitle(int position)` which is used to get the title for each tab and `getItem(int position)` which determines the fragment for each tab.

```java
static class PagerAdapter extends FragmentPagerAdapter {
    private final List<Fragment> myFragments = new ArrayList<>();
    private final List<String> myFragmentTitles = new ArrayList<>();
    private Context context;

    public PagerAdapter(FragmentManager fm, Context context) {
        super(fm);
        this.context = context;
    }

    public void addFragment(Fragment fragment, String title) {
        myFragments.add(fragment);
        myFragmentTitles.add(title);
    }

    @Override
    public Fragment getItem(int position) {
        return myFragments.get(position);
    }

    @Override
    public int getCount() {
        return myFragments.size();
    }

    @Override
    public CharSequence getPageTitle(int position) {
        return myFragmentTitles.get(position);
    }
}
```

### Setup Sliding Tabs

Finally, we need to attach our `ViewPager` to the `PagerAdapter` and then configure the sliding tabs with a two step process:

* In the `onCreate()` method of your activity, find the `ViewPager` and connect the adapter.
* Set the `ViewPager` on the `TabLayout` to connect the pager with the tabs.

```java
public class MainActivity extends AppCompatActivity {

    TabLayout tabLayout;
    ViewPager viewPager;
    PagerAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tabLayout = (TabLayout) findViewById(R.id.tab_layout);
        viewPager = (ViewPager) findViewById(R.id.pager);

        if (viewPager != null) {
            setupViewPager(viewPager);
        }
    }

    private void setupViewPager(ViewPager viewPager) {
        adapter = new PagerAdapter(getSupportFragmentManager(), this);
        adapter.addFragment(new ContentFragment().newInstance("Page1"), "Tab 1");
        adapter.addFragment(new ContentFragment().newInstance("Page2"), "Tab 2");
        adapter.addFragment(new ContentFragment().newInstance("Page3"), "Tab 3");
        viewPager.setAdapter(adapter);
        tabLayout.setupWithViewPager(viewPager);
    }
    //...
}
```
Heres the output:

![Screen 1](http://i.imgur.com/8hP2PgL.png?2)

### Customize Tab Indicator Color

Add style to `styles.xml`

```xml
<style name="MyCustomTabLayout" parent="Widget.Design.TabLayout">
      <item name="tabIndicatorColor">#ffff111b</item>
</style>
```

Then override this style for your TabLayout:

```xml
<android.support.design.widget.TabLayout
        android:id="@+id/tab_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        style="@style/MyCustomTabLayout"/>
```

### Add Icons to TabLayout

Add icon resource to `PagerAdapter` and `getPageTitle(position)` method as shown in the code snippet below.

```java
private int[] imageResId = {
      R.drawable.facebook,
      R.drawable.instagram,
      R.drawable.linkedin
};
      
//...

@Override
public CharSequence getPageTitle(int position) {
      Drawable image = context.getResources().getDrawable(imageResId[position]);
      image.setBounds(0, 0, image.getIntrinsicWidth(), image.getIntrinsicHeight());
      SpannableString sb = new SpannableString("  ");
      ImageSpan imageSpan = new ImageSpan(image, ImageSpan.ALIGN_BOTTOM);
      sb.setSpan(imageSpan, 0, 1, Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
      return sb;
}
      
```

By default, the tab created by TabLayout sets the `textAllCaps` property to be true, which prevents ImageSpans from being rendered.  You can override this behavior by changing the `tabTextAppearance` property.

```xml
<style name="MyCustomTabLayout" parent="Widget.Design.TabLayout">
    <item name="tabTextAppearance">@style/MyCustomTextAppearance</item>
    <item name="tabIndicatorColor">#ffff111b</item>
</style>

<style name="MyCustomTextAppearance" parent="TextAppearance.Design.Tab">
    <item name="textAllCaps">false</item>
</style>
```

Tabs with images:

![Slide 2](http://i.imgur.com/EYxKrpE.png?1)


### Add Custom View to TabLayout

We will show how to custom XML layout for each tab. First, we create a template Tab(custom_tab_item.xml).

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/tab_item_view"
        android:layout_width="24dp"
        android:layout_height="24dp" />

    <TextView
        android:id="@+id/tab_item_txt"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="test"/>

</LinearLayout>
```

To achieve this, iterate over all the `TabLayout.Tab`s after attaching the sliding tabs to the pager.

```java
public class MainActivity extends AppCompatActivity {

    TabLayout tabLayout;
    ViewPager viewPager;
    PagerAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tabLayout = (TabLayout) findViewById(R.id.tab_layout);
        viewPager = (ViewPager) findViewById(R.id.pager);

        if (viewPager != null) {
            setupViewPager(viewPager);
        }
    }

    private void setupViewPager(ViewPager viewPager) {
        adapter = new PagerAdapter(getSupportFragmentManager(), this);
        adapter.addFragment(new ContentFragment().newInstance("Page1"), "Tab 1");
        adapter.addFragment(new ContentFragment().newInstance("Page2"), "Tab 2");
        adapter.addFragment(new ContentFragment().newInstance("Page3"), "Tab 3");
        viewPager.setAdapter(adapter);
        tabLayout.setupWithViewPager(viewPager);

        // Iterate over all tabs and set the custom view
        for (int i = 0; i < tabLayout.getTabCount(); i++) {
            TabLayout.Tab tab = tabLayout.getTabAt(i);
            tab.setCustomView(adapter.getTabView(i));
        }
    }
    //...
}    
```

Next, we add the `getTabView(position)` method to the `PagerAdapter` class.

```java
static class PagerAdapter extends FragmentPagerAdapter {
    private final List<Fragment> myFragments = new ArrayList<>();
    private final List<String> myFragmentTitles = new ArrayList<>();
    private Context context;

    private int[] imageResId = {
            R.drawable.facebook,
            R.drawable.instagram,
            R.drawable.linkedin
    };
        
    public View getTabView(int position) {
        // Given you have a custom layout in `res/layout/custom_tab_item.xml` with a TextView and ImageView
        View v = LayoutInflater.from(context).inflate(R.layout.custom_tab_item, null);
        TextView tv = (TextView) v.findViewById(R.id.tab_item_txt);
        tv.setText(myFragmentTitles.get(position));
        ImageView img = (ImageView) v.findViewById(R.id.tab_item_view);
        img.setImageResource(customImageResId[position]);
        return v;
    }
}
  //...
```
Finally, we can setup any custom tab content for each page in the adapter.
![Slide 3](http://i.imgur.com/IERLy6R.png?1)


### Set PageChangeListener

```java
viewPager.setOnPageChangeListener(new ViewPager.OnPageChangeListener() {

    // This method will be invoked when a new page becomes selected.
    @Override
    public void onPageSelected(int position) {
        Toast.makeText(MainActivity.this,
                "Selected page position: " + position, Toast.LENGTH_SHORT).show();
    }

    // This method will be invoked when the current page is scrolled
    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
        // Code goes here
    }

    // Called when the scroll state changes:
    // SCROLL_STATE_IDLE, SCROLL_STATE_DRAGGING, SCROLL_STATE_SETTLING
    @Override
    public void onPageScrollStateChanged(int state) {
        // Code goes here
    }
});
```

## References

* <https://github.com/codepath/android_guides/wiki/Google-Play-Style-Tabs-using-TabLayout#design-support-library>
* <https://github.com/codepath/android_guides/wiki/ViewPager-with-FragmentPagerAdapter>





