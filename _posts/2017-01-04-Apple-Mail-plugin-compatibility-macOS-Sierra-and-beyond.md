---
layout: post
title:  "Apple Mail plugin compatibility - macOS Sierra and beyond"
date:   2017-01-04 14:30:00 +0800
tags: [macOS, mail, hubspot, applescript]
---
Apple Mail has a strict plugin compatibility policy that means that any time the Mail app is updated (such as during a significant OS version update) all plugins will be marked as incompatible. This is understandable as plugin development is not officially supported by Apple and the undocumented API may change significantly between versions.

However, in certain cases you might need to use a plugin that is no longer being developed. The Mail app stores the current Plugin Compatibility UUID in its own Info.plist, and for a plugin to be considered compatible it must reference this UUID in its own Info.plist.

The Plugin Compatibility UUID for the Mail app can be found as follows:
{% highlight shell %}
defaults read /Applications/Mail.app/Contents/Info.plist PluginCompatibilityUUID
{% endhighlight %}

The key to reference this UUID under in the plugin's Info.plist file depends on your version of macOS.    
For macOS (OS X) versions before 10.12, you need to use `SupportedPluginCompatibilityUUIDs`.    
For macOS 10.12 and above, the key format is `Supported<MajorVersion>.<MinorVersion>PluginCompatibilityUUIDs`. For example, on macOS 10.12.2, the key will be `Supported10.12PluginCompatibilityUUIDs`.

A quick way of setting this is to use `defaults`:
{% highlight shell %}
defaults write "<Plugin bundle path>/Contents/Info.plist Supported<MajorVersion><MinorVersion>PluginCompatibilityUUIDs -array-add <UUID>
{% endhighlight %}

Otherwise you can edit the plist file in Xcode or a similar editor.

I ran into this myself when trying to use the (HubSpot Sales) Sidekick plugin, which is no longer officially developed. I have [created an installer script](https://github.com/OscarBarrett/sidekick-plugin-installer) for anyone who might want to use this plugin on a new version of Mail or macOS.
