# Recovery

`RootRecovery`, accessed via `FMTCRoot.recovery`, allows access to the bulk download recovery system, which is designed to allow rescue (salvation and restarting) of failed downloads when they crashed due to an unexpected event.

{% embed url="https://pub.dev/documentation/flutter_map_tile_caching/latest/flutter_map_tile_caching/RootRecovery-class.html" %}

<pre class="language-dart" data-full-width="false"><code class="lang-dart"><strong>final rec = FMTCRoot.recovery;
</strong>
await rec.recoverableRegions; // List all recoverable regions, and whether each one has failed
await rec.recoverableRegions.failedOnly; // List all failed downloads
await stats.getRecoverableRegion(); // Retrieve a specific recoverable region by ID
await stats.cancel(); // Safely remove the specified recoverable region
</code></pre>

{% hint style="success" %}
The recovery system 'tracks' downloads to monitor which tiles have been successfully downloaded (and no longer buffered).

This allows the `DownloadableRegion.start` parameter to be adjusted to avoid redownloading tiles that were already downloaded.
{% endhint %}
