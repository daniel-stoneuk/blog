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

![Bottom Sheet Basic Design]({{site.baseurl}}/assets/material_design_spec_components_bottom_sheets.png)

Essentially, a modal bottom sheet that contains normal interactive elements rather then a list. I was glad to see that official bottom sheet support was added to the [Android Support Library 23.2][androidsupportlibrary232] but after looking further into the implementation, what I wanted didn't look as simple as I thought it would be.

[androidsupportlibrary232]: http://android-developers.blogspot.it/2016/02/android-support-library-232.html
