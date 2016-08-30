---
published: true
layout: post
date: '2016-08-26 21:06 +0200'
title: Android Bottom Sheet with Dimmed Background
categories:
  - android
---
So, I was developing a new budget feature for Monitor for EnergyHive and Engage and needed a way to display the data without obstructing the current user interface. 

I am going to do a complete redesign of the app sometime soon, but for now, this is how it looks:

![device-monitor-current.png]({{site.baseurl}}/assets/device-monitor-current.png){: .screenshot}

As you can see, the design I have used doesn't leave much space for extra features. I'm in desperate need of a good UI designer.

---

Anyway. As always, the [Google Material Design Spec][materialdesignspec] is a godsend for us developers who have no sense of style. I wanted to achieve something like the first image on the [bottom sheet][bottomsheetspec] with nice animations.

[materialdesignspec]: https://material.google.com
[bottomsheetspec]: https://material.google.com/components/bottom-sheets.html

![Bottom Sheet Basic Design]({{site.baseurl}}/assets/material_design_spec_components_bottom_sheets.png){: .screenshot}

Essentially, a modal bottom sheet that contains normal interactive elements rather then a list. I was glad to see that official bottom sheet support was added to the [Android Support Library 23.2][androidsupportlibrary232] but after looking further into the implementation, what I wanted didn't look as simple as I thought it would be. I created this: 

![Bottom Sheet Gif]({{site.baseurl}}/assets/bottomsheet.gif){: .screenshot}

![Bottom Sheet Gif]({{site.baseurl}}/assets/bottomsheethires.gif)


[androidsupportlibrary232]: http://android-developers.blogspot.it/2016/02/android-support-library-232.html


You see, the support library offered two options - the ability to create a bottom sheet out of any view inside a `CoordinatorLayout` & a `BottomSheetDialog` / `BottomSheetDialogFragment`.

At a glance, the `BottomSheetDialogFragment` is just what I wanted as it hosts a fragment in a bottom sheet, and dims the existing activity. But nope, instead of a smooth upward animation, I got a jarring activity transition exactly like the native sharing "animation" in stock android.

Ugly!

I started playing with `FrameLayouts`, and `TouchEvents` to try and get the animation you see above. I'll go through the steps I went through, and explain briefly. 

## Creating the Fragment

```java
public class InfoBottomSheetFragment extends Fragment implements LoaderManager.LoaderCallbacks<CostMonthForecast>, View.OnClickListener {

    public InfoBottomSheetFragment() {
        // Required empty public constructor
    }


    public static InfoBottomSheetFragment newInstance(String param1, String param2) {
        InfoBottomSheetFragment fragment = new InfoBottomSheetFragment();
        return fragment;
    }
    
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        View rootView = inflater.inflate(R.layout.fragment_info_bottom_sheet, container, false);
        
        // View references
        
        return rootView;
    }
    
    @Override
    public Loader<CostMonthForecast> onCreateLoader(int id, Bundle args) {
    	return new BudgetLoader(
                getActivity()
        	);
    }
    
    @Override
    public void onLoadFinished(Loader<CostMonthForecast> loader, CostMonthForecast data) {
    	// Update UI
    }
    
    public void setVisibility(boolean visible) {
        isVisible = visible;
        if (visible) {
            if (!budgetLoaderInit || error) {
                getLoaderManager().restartLoader(BUDGET_LOADER, null, this);
                budgetLoaderInit = true;
            }
        }
    }
}
```

I have ommitted most of the code, as it is unnecessary. I tend to use custom `AsyncTaskLoaders` to fetch data from the server. Here, I have inflated the views from the associated layout and created a public method that will allow me to communicate visibility from the `MainActivity` to the `Fragment`.

This may seem all very random but trust me, it will make sense later on.

## Adding the Bottom Sheet Fragment to the Main Activity

```xml
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/main_activity_coordinator_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <FrameLayout
        android:id="@+id/main_activity_fragment_scrim"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:foreground="@drawable/shape_window_dim"
        >

        <fragment xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            xmlns:tools="http://schemas.android.com/tools"
            android:name="com.danielstone.energyhive.MainActivityFragment"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            tools:layout="@layout/fragment_main"
            android:id="@+id/main_activity_fragment" />
    </FrameLayout>

	<!-- The NestedScrollView is the BottomSheet -->

    <android.support.v4.widget.NestedScrollView
        android:id="@+id/bottom_sheet"
        android:layout_width="match_parent"
        android:layout_height="@dimen/budget_fragment_height"
        android:elevation="16dp"
        android:clipToPadding="true"
        android:background="@color/colorWhite"
        app:layout_behavior="android.support.design.widget.BottomSheetBehavior"
        >

        <fragment xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            xmlns:tools="http://schemas.android.com/tools"
            android:name="com.danielstone.energyhive.InfoBottomSheetFragment"
            android:layout_width="match_parent"
            android:layout_height="@dimen/budget_fragment_height"
            tools:layout="@layout/fragment_main"
            android:id="@+id/info_bottom_sheet_fragment" />

    </android.support.v4.widget.NestedScrollView>

</android.support.design.widget.CoordinatorLayout>
```

As you can see above, I have wrapped the main fragment in a `FrameLayout` with it's `foreground` set to a drawable that is simply a black rectangle.

shape_window_dim.xml:
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle" >
    <solid android:color="#000000" />
</shape>
```

The Bottom Sheet
