# flutter\_map Integration

Stores also have the method `getTileProvider()`. This is the point of integration with flutter\_map, providing browse caching through a custom image provider. This `TileProvider` can then be passed to the `TileLayer.tileProvider` parameter.

```dart
import 'package:flutter_map/flutter_map.dart';

class MapView extends StatefulWidget {
    late final tileProvider = FMTCStore('storeName').getTileProvider();
    
    Widget build(BuildContext context) {
        return FlutterMap(
            // options: MapOptions(),
            children: [
                TileLayer(
                    // Other config parameters
                    tileProvider: tileProvider,
                ),
            ],
        );
    }
}
```

{% hint style="warning" %}
Avoid getting the `TileProvider` from within the build method, especially if the widget is rebuilt frequently.

It can cause unnecessary errors and worsened performance.
{% endhint %}

## Tile Provider Settings

This method (and others) optionally take a `FMTCTileProviderSettings`. These configure the behaviour of the tile provider. Defaults to the settings specified in the [Broken link](broken-reference "mention"), or the package default (see table below) if that is not specified.

`FMTCTileProviderSettings` can take the following arguments:

<table data-card-size="large" data-view="cards"><thead><tr><th>Parameter</th><th>Description</th><th>Default</th></tr></thead><tbody><tr><td><code>behavior</code>: <a href="fm-integration.md#cache-behavior"><code>CacheBehavior</code></a></td><td>Determine the logic used during handling storage and retrieval of browse caching</td><td><code>CacheBehavior.cacheFirst</code></td></tr><tr><td><code>cachedValidDuration</code>: <code>Duration</code></td><td>Length of time a tile remains valid, after which it must be fetched again (ignored in <code>onlineFirst</code> mode)</td><td><code>const Duration(days: 16)</code></td></tr><tr><td><code>maxStoreLength</code>: <code>int</code></td><td>Maximum number of tiles allowed in a cache store (deletes oldest tile)</td><td><code>0</code>: disabled</td></tr><tr><td><code>obscuredQueryParams</code>: <code>List&#x3C;String></code></td><td>See <a data-mention href="fm-integration.md#obscuring-query-parameters">#obscuring-query-parameters</a></td><td><code>[]</code>: empty</td></tr></tbody></table>

### Cache Behavior

This enumerable contains 3 values, which are used to dictate which logic should be used to store and retrieve tiles from the store.

<table><thead><tr><th width="174">Value</th><th>Explanation</th></tr></thead><tbody><tr><td><code>cacheFirst</code></td><td><p>Get tiles from the local cache if possible.</p><p>Only uses the Internet if it doesn't exist, or to update it if it has expired.</p></td></tr><tr><td><code>onlineFirst</code></td><td><p>Get tiles from the Internet if possible.</p><p>Updates every cached tile every time it is fetched (ignores expiry).</p></td></tr><tr><td><code>cacheOnly</code></td><td><p>Only get tiles from the local cache, and throw an error if not found.</p><p>Recommended for dedicated offline modes.</p></td></tr></tbody></table>

### Obscuring Query Parameters

{% hint style="info" %}
Since v3, FMTC relies on URL equality to find tiles within a store during browsing. This method is therefore necessary in some cases where the URL contains query parameters.
{% endhint %}

If the URL's query parameters (the key-value pairs list found after the '?') contains a value that may change between fetches, such as an API key, use `obscuredQueryParams`.

This method strips specified keys and values from the query parameters, and avoids storing them in the database.

Pass it the list of query keys who's values need to be omitted from storage.\
For example, 'api\_key' would remove the 'api\_key', and any other characters until the next key-value pair, or the end of the URL, as seen below:

<pre><code>https://tile.myserver.com/{z}/{x}/{y}?api_key=001239876&#x26;mode=dark
<strong>https://tile.myserver.com/{z}/{x}/{y}?&#x26;mode=dark
</strong></code></pre>

{% hint style="warning" %}
Do not depend on this method to remove secret information from a URL.
{% endhint %}

{% hint style="warning" %}
Prefer sending any information (as discussed above) through the HTTP headers. This may improve performance and reliability, and can be considered good practise anyhow.
{% endhint %}

## Check If A Tile Is Cached

{% embed url="https://pub.dev/documentation/flutter_map_tile_caching/latest/flutter_map_tile_caching/FMTCTileProvider/checkTileCached.html" %}
