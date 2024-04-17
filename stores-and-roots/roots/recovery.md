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

## Restoring Usable Regions

Once a `RecoveredRegion` has been retreived, it can be converted to a standard region:

* either a `DownloadableRegion`, using `toDownloadable`\
  The `start` tile will be adjusted from the original to reflect the progress of the download before it failed, meaning that tiles already successfully cached (excluding buffered) will not be downloaded again, saving time and data!\
  The `end` tile will be either the original, or the maximum number of tiles normally in the region (which will have no resulting difference than `null`, but allows for a quick estimate of the number of remaining tiles to be made without needing to re`check` the entire region).
* or a `BaseRegion` (with the correct subtype), using `toRegion`
