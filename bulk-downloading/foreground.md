# 3⃣ Start In Foreground

Now that you have constructed a `DownloadableRegion`, you're almost ready to go.

## Customization Options

Before you call `startForeground` to start the download, check out the customization parameters:

{% embed url="https://pub.dev/documentation/flutter_map_tile_caching/latest/flutter_map_tile_caching/DownloadManagement/startForeground.html" %}

* `parallelThreads` (defaults to 5)\
  The number of simultaneous download threads to run
* `maxBufferLength` (defaults to 200)\
  The number of tiles to temporarily persist in memory before writing to the cache
* `skipExistingTiles` (defaults to `true`)\
  Whether to avoid re-downloading tiles that have already been cached
* `skipSeaTiles` (defaults to `true`)\
  Whether to avoid caching tiles that are entirely sea
* `rateLimit`\
  The maximum number of tiles that can be attempted per second
* `maxReportInterval` (defaults to 1 second)\
  The duration in which to emit _at least_ one `DownloadProgress` event

## Start Download

{% hint style="warning" %}
Before using FMTC, especially to bulk download, ensure you comply with the appropriate restrictions and terms of service set by your tile server. Failure to do so may lead to any punishment, at the tile server's discretion.

This library and/or the creator(s) are not responsible for any violations you make using this package.

***

Some common tile servers' ToS are listed below:

* [OpenStreetMap](https://operations.osmfoundation.org/policies/tiles)
* [Mapbox](https://www.mapbox.com/legal/tos)
* [Thunderforest](https://www.thunderforest.com/terms/)
* [Stadia Maps](https://stadiamaps.com/terms-of-service/)

For testing purposes, check out the testing tile server included in the FMTC project: [#testing-your-application](introduction.md#testing-your-application "mention").
{% endhint %}

```dart
final progressStream = FMTC.instance('storeName').download.startForeground(
    region: downloadableRegion,
    // parallelThreads: 10,
    // rateLimit: 200,
);
```

{% embed url="https://pub.dev/documentation/flutter_map_tile_caching/latest/flutter_map_tile_caching/DownloadManagement/startForeground.html" %}

{% hint style="info" %}
Whilst not recommended, it is possible to start and control multiple downloads simultaneously, by using a unique `Object` as the `instanceId` argument. This 'key' can then later be used to control its respective download instance.

Note that this option may be unstable.
{% endhint %}

## Listen For Progress

The `startForeground` method returns a (non-broadcast) `Stream` of `DownloadProgress` events, which contain information about the overall progress of the download, as well as the state of the latest tile attempt.

{% embed url="https://pub.dev/documentation/flutter_map_tile_caching/latest/flutter_map_tile_caching/DownloadProgress-class.html" %}

<mark style="background-color:orange;">\<link to</mark> <mark style="background-color:orange;"></mark><mark style="background-color:orange;">`TileEvent`</mark> <mark style="background-color:orange;"></mark><mark style="background-color:orange;">api docs here></mark>

To reflect the information from a single event back to the user, use a `StreamBuilder`, and build the UI dependent on the 'snapshots' of the stream. If you need to keep track of information from across multiple events, see [#keeping-track-across-events](foreground.md#keeping-track-across-events "mention") below.

### Keeping Track Across Events

In addition to display each individual event to your user, you may also need to keep track of information from across multiple `DownloadProgress` events. In this case, you'll likely need to use the `latestTileEvent` getter to access the latest `TileEvent` object, and keep track of its properties.

For example, you may wish to keep a list of all the failed tiles' URLs.

However, there are 3 important things to keep in mind when doing this:

{% hint style="warning" %}
**Memory**

Avoid keeping a list of _all_ emitted events. Instead, keep a 'circular buffer' of the useful subset of events.

A single download can have many events, and storing them all will consume a lot of memory. It is easy to consume all of the remaining allocated memory, and crash the app.
{% endhint %}

{% hint style="warning" %}
**Data Loss**

Avoid keeping track of required information internally through a `StreamBuilder` intended to display a UI.

A `StreamBuilder` will not necessarily call the `builder` callback once per event, especially if the download has a high TPS. Therefore, events may be lost.
{% endhint %}

{% hint style="warning" %}
**Data Duplication**

Avoid keeping track of events where the `latestTileEvent.isRepeat` property is `true`.

These `TileEvents` are exact repeats of the previous event, usually due to the `maxReportInterval` functionality. Therefore, including both in a dataset would be erroneous.
{% endhint %}
