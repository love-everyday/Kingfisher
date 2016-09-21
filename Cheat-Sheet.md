> #### Feel free to copy & paste code below.

This documentation will describe some most common usage of Kingfisher. The code snippet is based on iOS. However, the similar code should also work for other platforms like macOS, by replacing the corresponding class (such as `UIImage` to `NSImage`, etc).

### Working with Kingfisher extensions for UI component

Some convenient extensions are supplied in Kingfisher. It covers most used features, and supports all regular image related UI components: `UIImageView`, `NSImageView`, `UIButton` and `NSButton`. We recommend to use these methods to keep your code simple.

#### Simply set an image from url to an image view

```swift
let url = URL(string: "https://domain.com/image.jpg")!
imageView.kf.setImage(with: url)
```

> Kingfisher will try to get the image from cache first. If not found, download and cache it for later use.
> 
> The `absoluteString` of `url` will be used as the cache key by default.

#### Use a specified key other than the url for cache

```swift
let resource = ImageResource(downloadURL: url, cacheKey: "my_cache_key")
imageView.kf.setImage(with: resource)
```

#### With a placeholder image while downloading

```swift
let image = UIImage(named: "default_profile_icon")
imageView.kf.setImage(with: url, placeholder: image)
```

#### With a completion handler

```swift
imageView.kf.setImage(with: url, completionHandler: { 
    (image, error, cacheType, imageUrl) in
    // image: Image? `nil` means failed
    // error: NSError? non-`nil` means failed
    // cacheType: CacheType
    //                  .none - Just downloaded
    //                  .memory - Got from memory cache
    //                  .disk - Got from memory Disk
    // imageUrl: URL of the image
})
```

#### With a loading indicator while downloading

```swift
imageView.kf.indicatorType = .activity
imageView.kf.setImage(with: url)
```

#### Use your own GIF file or any image as the indicator image while downloading

```swift
let p = Bundle.main.path(forResource: "loader", ofType: "gif")!
let data = try! Data(contentsOf: URL(fileURLWithPath: p))

imageView.kf.indicatorType = .image(imageData: data)

imageView.kf.setImage(with: url)
```

#### Customize the indicator with any view you want

```swift
struct MyIndicator: Indicator {
    let view: UIView = UIView()
    
    func startAnimatingView() { view.isHidden = false }
    func stopAnimatingView() { view.isHidden = true }
    
    init() {
        view.backgroundColor = .red
    }
}

let i = MyIndicator()
imageView.kf.indicatorType = .custom(indicator: i)
```

#### Or update your own indicator UI with progress block

```swift
imageView.kf.setImage(with: url, progressBlock: {
    receivedSize, totalSize in
    let percentage = (Float(receivedSize) / Float(totalSize)) * 100.0
    print("downloading progress: \(percentage)%")
    myIndicator.percentage = percentage
})
```

#### Add a fade transition when setting image after downloaded

```swift
imageView.kf.setImage(with: url, options: [.transition(.fade(0.2))])
```

#### Transform downloaded image to round corner before displaying and caching

```swift
let processor = RoundCornerImageProcessor(cornerRadius: 20)
imageView.kf.setImage(with: url, placeholder: nil, options: [.processor(processor)])
```

> You can also use other processor to get blur/tint/black & white or some other effect.

#### Apply multiple processor before setting the image

```swift
let processor = BlurImageProcessor(blurRadius: 4) >> RoundCornerImageProcessor(cornerRadius: 20)
imageView.kf.setImage(with: url, placeholder: nil, options: [.processor(processor)])
```

#### Skip cache searching, force downloading image again 

```swift
imageView.kf.setImage(with: url, options: [.forceRefresh])
```

#### Only search cache for the image, do not download if not existing

```swift
imageView.kf.setImage(with: url, options: [.onlyFromCache])
```

#### For `UIButton` & `NSButton`

```swift
let uiButton: UIButton = //...
uiButton.kf.setImage(with: url, for: .normal, placeholder: nil, options: nil, progressBlock: nil, completionHandler: nil)
uiButton.kf.setBackgroundImage(with: url, for: .normal, placeholder: nil, options: nil, progressBlock: nil, completionHandler: nil)

let nsButton: NSButton = //...
nsButton.kf.setImage(with: url, placeholder: nil, options: nil, progressBlock: nil, completionHandler: nil)
nsButton.kf.setAlternateImage(with: url, placeholder: nil, options: nil, progressBlock: nil, completionHandler: nil)
```

### Cache & Downloader

Kingfisher is composed with two main component: an `ImageDownloader` for downloading images, and an `ImageCache` to manipulate the cache. You can use either of them separately.

#### Use `ImageDownloader` to download an image without caching

```swift
ImageDownloader.default.downloadImage(with: url, options: [], progressBlock: nil) {
    (image, error, url, data) in
    print("Downloaded Image: \(image)")
}
```

#### Use `ImageCache` to store or get images

```swift
let image: UIImage = //...
ImageCache.default.store(image, forKey: "key_for_image")

let anotherImage: UIImage = //...
let imageData = //.. Data from anotherImage
ImageCache.default.store(anotherImage, 
                         original: imageData, 
                         forKey: "key_for_another_image", 
                         toDisk: false)

ImageCache.default.isImageCached(forKey: "key_for_image")
// (cached: true, cacheType: .memory)
ImageCache.default.isImageCached(forKey: "key_for_another_image")
// (cached: true, cacheType: .memory)

// Force quit and relaunch
ImageCache.default.isImageCached(forKey: "key_for_image")
// (cached: true, cacheType: .disk)
ImageCache.default.isImageCached(forKey: "key_for_another_image")
// (cached: false, cacheType: .none)

ImageCache.default.retrieveImage(forKey: "key_for_image", options: nil) { 
    image, cacheType in
    if let image = image {
	    print("Get image \(image), cacheType: \(cacheType).")
	    //In this code snippet, the `cacheType` is .disk
    } else {
       print("Not exist in cache.")
    }
}
```

#### Remove a cached image

```swift
// From both memory and disk
ImageCache.default.removeImage(forKey: "key_for_image")

// Only from memory
ImageCache.default.removeImage(forKey: "key_for_image", fromDisk: false)
```


#### Set maximum disk cache size for default cache

```swift
// 50 MB
ImageCache.default.maxDiskCacheSize = 50 * 1024 * 1024
// Default value is 0, which means no limit.
```

> The `default` downloader will be used by image extension methods, if no customized one set.

#### Get used size of disk for a cache

```swift
ImageCache.default.calculateDiskCacheSize { size in
    print("Used disk size by bytes: \(size)")
}
```

#### Clear cache manually

```swift
// Clear memory cache right away.
cache.clearMemoryCache()

// Clear disk cache. This is an async operation.
cache.clearDiskCache()

// Clean expired or size exceeded disk cache. This is an async operation.
cache.cleanExpiredDiskCache()
```

> Kingfisher will purge the memory cache when received a memory warning, as well as clean the expired and size exceeded cache when needed. Normally there is no need to clean cache yourself. These methods exist in case of you want your users have more control on the cache.

#### Set longest time duration of the cache being stored in disk

```swift
// 3 days
ImageCache.default.maxDiskCacheSize = 60 * 60 * 24 * 3
// Default value is 60 * 60 * 24 * 7, which means 1 week.
```

#### Set timeout duration for default image downloader

```swift
// 30 second
ImageDownloader.default.downloadTimeout = 30.0
// Default value is 15.
```

#### Use customized downloader and cache instead of the default ones

```swift
let downloader = ImageDownloader(name: "huge_image_downloader")
downloader.downloadTimeout = 150.0
let cache = ImageCache(name: "longer_cache")
cache.maxDiskCacheSize = 60 * 60 * 24 * 30

imageView.kf.setImage(with: url, options: [.downloader(downloader), .targetCache(cache)])
```

#### Cancel a downloading or retriving task

```swift
// In table view data source
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    //...
    cell.imageView.kf.setImage(with: url)
    //...
}

// In table view delegate
func tableView(_ tableView: UITableView, didEndDisplaying cell: UITableViewCell, forRowAt indexPath: IndexPath) {
	//...
	cell.imageView.kf.cancelDownloadTask()
}
```

#### Modify a request before sending

```swift
let modifier = AnyModifier { request in
    var r = request
    r.setValue("", forHTTPHeaderField: "Access-Token")
    return r
}       
imageView.kf.setImage(with: url, placeholder: nil, options: [.requestModifier(modifier)])
```

#### Authentication with `NSURLCredential`

```swift
// In ViewController
ImageDownloader.default.authenticationChallengeResponder = self

extension ViewController: AuthenticationChallengeResponsable {
    func downloader(_ downloader: ImageDownloader,
            didReceive challenge: URLAuthenticationChallenge,
               completionHandler: (URLSession.AuthChallengeDisposition, URLCredential?) -> Void)
    {
        // Provide your `AuthChallengeDisposition` and `URLCredential`
        let disposition: URLSession.AuthChallengeDisposition = // ..
        let credential: URLCredential? = //..
        completionHandler(disposition, credential)
    }
}
```

#### Check HTTP status code and determine whether it is valid

```swift
// In ViewController
ImageDownloader.default.delegate = self

extension ViewController: ImageDownloaderDelegate {
    func isValidStatusCode(_ code: Int, for downloader: ImageDownloader) -> Bool {
        return code == 200 || code == 202
    }
}
```

> An `invalidStatusCode` error will be raised if `false` is returned in this delegate method.

#### Use your own session configuration in a downloader

```swift
let imageDownloader: ImageDownloader = //...

// A configuration without persistent storage for caches is 
// requsted for downloader working correctly.
imageDownloader.sessionConfiguration = //...
```

### Processor

`ImageProcessor` is more likly a transformer of image. It convert some data to an image or an image to another. You can supply a processor to `ImageDownloader`. The downloader will apply it to downloaded data/images, then send the processed images to the image view or cache if needed.

#### Use the default processor

```swift
// Just without anything
imageView.kf.setImage(with: url)
// It equals to
imageView.kf.setImage(with: url, options: [.processor(DefaultImageProcessor.default)])
```

> `DefaultImageProcessor` converts downloaded data to a corresponded image object. PNG, JPEG and GIF are supported by default.

#### Built-in processors of Kingfisher

```swift
// Round corner
let processor = RoundCornerImageProcessor(cornerRadius: 20)

// Resizing
let processor = ResizingImageProcessor(targetSize: CGSize(width: 100, height: 100))

// Blur with a radius
let processor = BlurImageProcessor(blurRadius: 5.0)

// Overlay with a color & fraction
let processor = OverlayImageProcessor(overlay: .red, fraction: 0.7)

// Tint with a color
let processor = TintImageProcessor(tint: .blue)

// Adjust color
let processor = ColorControlsProcessor(brightness: 1.0, contrast: 0.7, saturation: 1.1, inputEV: 0.7)

// Black & White
let processor = BlackWhiteProcessor()

imageView.kf.setImage(with: url, options: [.processor(processor)])
```

#### Concatenating processors

```swift
// Blur and then make round corner
let processor = BlurImageProcessor(blurRadius: 5.0) >> RoundCornerImageProcessor(cornerRadius: 20)
imageView.kf.setImage(with: url, options: [.processor(processor)])

// `>>` equals the `append(another:)` method of `ImageProcessor`.
// Above equals to:
let processor = BlurImageProcessor(blurRadius: 5.0).append(RoundCornerImageProcessor(cornerRadius: 20))
```

#### Create and use your own processor

Make a type adopting to `ImageProcessor` and implement `identifier` and `process`:

```swift
struct WebpProcessor: ImageProcessor {
    // `identifier` should be the same for processors with same properties/functionality
    // It will be used when storing and retrieving the image to/from cache.
    let identifier = "com.yourdomain.webpprocessor"
    
    // Convert input data/image to target image and return it.
    func process(item: ImageProcessItem, options: KingfisherOptionsInfo) -> Image? {
        switch item {
        case .image(let image):
            print("already an image")
            return image
        case .data(let data):
            return WebpFramework.createImage(from: webpData)
        }
    }
}

// Then pass it to the `setImage` methods:
let processor = WebpProcessor()
let url = URL(string: "https://yourdomain.com/example.webp")
imageView.kf.setImage(with: url, options: [.processor(processor)])
```

#### Create a processor from `CIFilter`

If you have a prepared `CIFilter`, you can create a processor quickly from it.

```swift
struct MyCIFilter: CIImageProcessor {

    let identifier = "com.yourdomain.myCIFilter"
    
    let filter = Filter { input in
        guard let filter = CIFilter(name: "xxx") else { return nil }
        filter.setValue(input, forKey: kCIInputBackgroundImageKey)
        return filter.outputImage
    }
}
```

#### Use processor with `ImageCache`

Although the `process` method of a `ImageProcessor` is only used in `ImageDownloader` to process the image right after the downloading, the `identidier` will be used when caching the processed images. Without the `identidier`, Kingfisher will not be able to tell which is the correct image in cache (Think about you have to store two version of an image from the same url, one should be filtered and another should be kept original).

When you use the extension methods of Kingfisher, please make sure to pass the `ImageProcessor` with options correctly, and Kingfisher will handle other things for you. However, there might be a chance that you need to interact with `ImageCache` yourself, for examle, to check whether a processed image already cached or not. In such situation, besides of passing in the key, you also need to supply the `identidier`:

```swift
let processor = WebpProcessor()
let url = URL(string: "https://yourdomain.com/example.webp")
imageView.kf.setImage(with: url, options: [.processor(processor)])

// Later
ImageCache.default.isImageCached(
         forKey: url.cacheKey, 
         processorIdentifier: processor.identifier)
```

### Serializer

`CacheSerializer` will be used to convert some data to an image object for retrieving from disk cache and vice versa for storing to disk cache.

#### Use the default serializer

```swift
// Just without anything
imageView.kf.setImage(with: url)
// It equals to
imageView.kf.setImage(with: url, options: [.cacheSerializer(DefaultCacheSerializer.default)])
```

> `DefaultCacheSerializer` converts cached data to a corresponded image object and vice versa. PNG, JPEG and GIF are supported by default.

#### Create and use your own serializer

Make a type adopting to `CacheSerializer` and implement `data(with:original:)` and `image(with:options:)`:

```swift
struct WebpCacheSerializer: CacheSerializer {
    func data(with image: Image, original: Data?) -> Data? {
        return WebpFramework.webpData(of: image)
    }
    
    func image(with data: Data, options: KingfisherOptionsInfo?) -> Image? {
        return WebpFramework.createImage(from: webpData)
    }
}

// Then pass it to the `setImage` methods:
let serializer = WebpCacheSerializer()
let url = URL(string: "https://yourdomain.com/example.webp")
imageView.kf.setImage(with: url, options: [.cacheSerializer(serializer)])
```

### Prefetch

You could prefetch some images and cache them before you display them on the screen. This is useful when you know a list of image resources you know they would probably be shown later.

```swift
let urls = ["http://example.com/image1.jpg", "http://example.com/image2.jpg"]
           .map { URL(string: $0)! }
let prefetcher = ImagePrefetcher(urls: urls) {
    skippedResources, failedResources, completedResources in
    print("These resources are prefetched: \(completedResources)")
}
prefetcher.start()

// Later when you need to display these images:
imageView.kf.setImage(with: urls[0])
anotherImageView.kf.setImage(with: urls[1])
```

### Animated GIF

```swift
let imageView = AnimatedImageView()
imageView.kf.setImage(with: URL(string: "your_animated_gif_image_url")!)
```

> Use `AnimatedImageView` instead of regular image view to display GIF in a more efficient way. It will only decode several frames of your GIF image to get a smaller memory footprint. You can set the frame count you need to pre-load by setting the framePreloadCount property of an `AnimatedImageView` (default is 10).

### Useful image extensions

Some processor/filter code is exposed, that means you can apply it seperately on an image regardless where it comes from. They are listed as below.

```swift
extension Image {
    public func kf.image(withRoundRadius radius: CGFloat, fit size: CGSize, scale: CGFloat) -> Image { }
    public func kf.resize(to size: CGSize) -> Image { }
    public func kf.blurred(withRadius radius: CGFloat) -> Image { }
    public func kf.overlaying(with color: Color, fraction: CGFloat) -> Image { }
    public func kf.tinted(with color: Color) -> Image { }
    public func kf.adjusted(brightness: CGFloat, contrast: CGFloat, saturation: CGFloat, inputEV: CGFloat) -> Image { }
    public func kf.apply(_ filter: Filter) -> Image { }
}
```

Please also see the full [API Reference](http://cocoadocs.org/docsets/Kingfisher/) to find out more.

