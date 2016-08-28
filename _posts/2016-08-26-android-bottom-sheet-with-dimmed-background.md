---
published: false
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

Anyway. As always, the [Google Material Design Spec][materialdesignspec] is a godsend for us developers who have no sense of style. I wanted to achieve something like the first image on the [bottom sheet][bottomsheetspec].

[materialdesignspec]: https://material.google.com
[bottomsheetspec]: https://material.google.com/components/bottom-sheets.html

![Bottom Sheet Basic Design]({{site.baseurl}}/assets/material_design_spec_components_bottom_sheets.png){: .screenshot}

Essentially, a modal bottom sheet that contains normal interactive elements rather then a list. I was glad to see that official bottom sheet support was added to the [Android Support Library 23.2][androidsupportlibrary232] but after looking further into the implementation, what I wanted didn't look as simple as I thought it would be.

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







