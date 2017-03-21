Kingfisher 2.0 contains breaking changes if you want to upgrade from 1.x version. We want our users to keep using their code as much as possible. But it is more important to keep this framework maintainable and clean for us. In version 2.0, we removed the old way of setting options in `KingfisherManager`. Now, all the `KingfisherManager.Options` related code is removed from the framework, and adopted to using `KingfisherOptionsInfo` items instead.

You can find more detail and some sample code in this guide, to help you transiting your code from 1.x to 2.0

### Breaking changes:

* The type of `KingfisherManager.Options` has been removed from the framework.
* The type of `KingfisherOptions` has been removed from the framework.
* As `KingfisherManager.Options` removed, `KingfisherManager.OptionsNone` and `KingfisherManager.DefaultOptions` has been removed as well.
* The options previously in `KingfisherOptions` now have benn moved as a `KingfisherOptionsInfoItem`. For example, `forceRefresh` now is `.ForceRefresh`. See the corresponding table below to see all options removed.
* `.Options` in `KingfisherOptionsInfoItem` has been removed since all options there is now could be set directly with `KingfisherOptionsInfoItem`.
* All methods using `Kingfisher.Options` as an argument now expect `KingfisherOptionsInfo` instead. There are two public methods in `ImageCache` and `ImageDownloader` involved by this. Here is a list:
	* `retrieveImageForKey(_:options:completionHandler:)` in `ImageCache`
	* `downloadImageWithURL(_:options:progressBlock:completionHandler:)` in `ImageDownloader`


### Migration

If you are only using the `UIImageView` extension of Kingfisher without any options, there is nothing you have to do when migrate to 2.0. However, if you have set some `KingfisherOptions` when using this framework, you may need to want check the corresponding items in `KingfisherOptionsInfoItem` and use them instead.

Here is a list for the relationship between `KingfisherOptions` and `KingfisherOptionsInfoItem`:

| KingfisherOptions  | KingfisherOptionsInfoItem                          |
| ------------------ | -------------------------------------------------  |
| None               |  (Just pass a nil)                                 |
| LowPriority  		|	.DownloadPriority(Float)                          |
| CacheMemoryOnly  	|	.CacheMemoryOnly                                  |
| BackgroundDecode  	|	.BackgroundDecode                                 |
| BackgroundCallback |	.CallbackDispatchQueue(dispatch_queue_t?)         |
| ScreenScale     	|	.ScaleFactor(CGFloat)                             |

Take an example. If you have a method like this before: 

```swift
let cache: ImageCache = //...
cache.retrieveImageForKey(
	key, options: KingfisherManager.OptionsNone, completionHandler: { 
	    (image, type) -> () in
	}
```

Now it should be:

```swift
let cache: ImageCache = //...
cache.retrieveImageForKey(
	key, options: nil, completionHandler: { 
	    (image, type) -> () in
	}
```

The same if you set some options:

```swift
let optionInfo: KingfisherOptionsInfo = [
    .Options([.ForceRefresh, .LowPriority, .BackgroundCallback]), 
    .Transition(ImageTransition.Fade(1))
    ]

imageView.kf_setImageWithURL(URL, 
          placeholderImage: nil,
               optionsInfo: optionInfo,
             progressBlock: { receivedSize, totalSize in
             },
         completionHandler: { image, error, cacheType, imageURL in
         })
```

Now it should be:

```swift
let queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)
let optionInfo: KingfisherOptionsInfo = [
    .ForceRefresh,
    .DownloadPriority(0.5),
    .CallbackDispatchQueue(queue),
    .Transition(ImageTransition.Fade(1))
]

imageView.kf_setImageWithURL(URL, 
          placeholderImage: nil,
               optionsInfo: optionInfo,
             progressBlock: { receivedSize, totalSize in
             },
         completionHandler: { image, error, cacheType, imageURL in
         })
```

### Help

If you have any additional questions around migration, feel free to open a documentation issue on Github to get more clarity added to this guide.
