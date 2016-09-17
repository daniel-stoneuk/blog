---
published: true
layout: post
date: '2016-09-17 23:14 +0100'
title: Amazon Dash with Tasker
---
## Amazon-Hyphen. 

So, you've probably seen the [Amazon Dash Button][dash] - a compact little wifi connected button. There have been loads of different "hacks" circulating on the internet, and since they were recently released in the UK I decided to order one.

One day later I had it toggling my Lifx light bulb thanks to the countless tutorials on the internet. I followed [Steven Tso's blog post][tso] and it only took me about half an hour.

Setting up the Dash Button with the app was simple, and cancelling at the final step (selecting the product) let's you press it without ordering anything. Since the Dash only connects to WiFi when it's pressed, the majority of the "hacks" online involved monitoring the WiFi network, waiting for the Dash Button to join, and then executing the relevant code.

Works great... Until your internet dies. I don't know about you but I have to restart my WiFi router almost daily to avoid slowing down, which seems to break the ARP monitoring code. When trying it again today, I realised that I could utilise the pointless notification that appeared on my phone instead of ignorantly swiping it away. It works quicker, and works whenever my phone is on and connected to the internet. Essentially 24/7.

![amazondash.gif]({{site.baseurl}}/assets/amazondash.gif)

#How?

As tasker has a plethora of plugins, I reinstalled it for the first time after purchasing it two years ago. Great - it hasn't changed a bit. From here what I did was:
1. Listen for a notification from Amazon Shopping
2. Remove this notification
3. Run plugin to toggle lights (or do anything with tasker)
4. Notify that the lights were toggled.

Nice and simple. I'll walk you through each step with the screenshots below.

[dash]:	https://www.amazon.com/Dash-Buttons/b?ie=UTF8&node=10667898011
[tso]: http://steventso.com/amazon-dash-lifx/
