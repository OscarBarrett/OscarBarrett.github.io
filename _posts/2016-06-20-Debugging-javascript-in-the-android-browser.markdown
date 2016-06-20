---
layout: post
title:  "Debugging JavaScript in the Android Browser"
date:   2016-06-20 14:04:00 +0800
tags: [development, debugging, mobile]
---
We use [TrackJS](https://trackjs.com/) to track and monitor JavaScript errors across more than a dozen websites, and recently we noticed an error that had fired just under 100 times in a short period of time for a single user.

A quick look at the captured information and there appeared to be problem with an older version of Android, in this case 4.0.3. Unfortunately the error was a rather unhelpful `undefined is not a function` triggered on minified vendor code. The user wasn't performing any action such as a click (which TrackJS will report), so all signs pointed towards a timeout or scroll-based event. The browser was reported as the Android Browser, a default browser that has slowly been phased out since Android 4.

I didn't have any physical device to test on so my next option was an emulator. [Android Studio](https://developer.android.com/studio/index.html) allows you to create virtual devices all the way from Android 6 down to Android 1. I created a virtual device matching the device resolution and Android version, and spun it up.

When your virtual device is running, you can use `adb` to tail logcat logs for the browser like so:
{% highlight shell %}
adb logcat browser:* *:S  
{% endhighlight %}

(Note: If `adb` is not in `$PATH` then you should be able to find it under the location where your android SDK is installed - for example, on OSX: `~/Library/Android/sdk/platform-tools/`)

Once you're tailing the log you can simply navigate to a webpage in the Android Browser and see the console messages coming through. Within the virtual device `10.0.2.2` will map to `localhost` on your machine, which can be used to debug a local web server.

After debugging the unminified code I found that the error was coming from a 3rd-party package that assumed the existence of some form of `requestAnimationFrame`, which the Android Browser doesn't support until 4.4. [One polyfill later](https://github.com/cagosta/requestAnimationFrame) and the issue has been resolved.
