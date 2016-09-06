Kingfisher 3.0 contains breaking changes since the world of Swift 3.0 is so different from Swift 2.x.

Once you decided to migrate your project to Swift 3, you have to also upgrade to Kingfisher 3, or you cannot keep using this framework in your project.

### Basic

The most noticeable change is almost all APIs in Kingfisher now are following the [Swift 3 API Design Guidelines](https://swift.org/blog/swift-3-api-design/). Although we want our users to keep using their code as much as possible, the gap between Swift 2 to Swift 3 is so huge which prevents us to do so. By upgrading to Kingfisher 3, you could use Kingfisher in a more Swifty way, but you have to spend some time to do the migration. 

Depending on how did you use Kingfisher in your project, the migration progress may take several minutes to maybe half an hour. Things will be quite simple if you were just using the extension methods. Just change your original calling of image set method:

```swift
let URL = NSURL(string: "http://domain.com/image.png")!
imageView.kf_setImageWithURL(URL, 
                placeholderImage: nil,
                     optionsInfo: [.Transition(ImageTransition.Fade(1))],
                   progressBlock: nil,
               completionHandler: nil)
```

to follow the new API design in Swift 3:

```swift
let url = URL(string: "http://domain.com/image.png")!
imageView.kf_setImage(with: url, 
               placeholder: nil,
                   options: [.transition(.fade(1))],
             progressBlock: nil,
         completionHandler: nil)
```

If you are using other APIs from Kingfisher, you may want to check the API Diff part below, to modify your original code to follow new APIs.

### API Diff

The full of API changes are listed below. Since Kingfisher is following Swift 3 API guidelines, you might be able to do the migration just following the guideline and ignore the diff documentation here. But we recommend to do a search and double check you are using the correct new APIs for your project.

#### General

##### Enum Member

![][modify]

All enum member now follow a camel naming with first letter lowercase. For example,

Before:

```swift
enum KingfisherOptionsInfoItem {
    case TargetCache(ImageCache)
    ...
}
```

Now changed to :

```swift
enum KingfisherOptionsInfoItem {
    case targetCache(ImageCache)
    ...
}
```

This affects all enum type in Kingfisher, including: `KingfisherOptionsInfoItem`, `CacheType`, `KingfisherError` and `ImageTransition`.

---

##### Types

Now all foundation types in Kingfisher are value-sematic version of these types. From Kingfisher 3, `URL`, `Data` or `URLSession` are used, instead of their class-varieties with `NS` prefix (`NSURL`, `NSData` or `NSURLSession`).

#### Resource

![][modify]

Before:

```swift
struct Resource { ... }
```

Now: 

```swift
struct ImageResource { ... }
```

---

![][add]

```swift
protocol Resource { .... }

```

> A representation for resource could be downloaded/cached by Kingfisher, in which `cacheKey` and `downloadURL` are defined.

---

![][add]

```swift
extension URL: Resource { ... }
```

> `URL` now conforms to `Resource`, so you could use it directly in Kingfisher extension APIs.

---

#### Extensions

##### UIImageView / NSImageView Extensions

![][modify]

Before:

```swift
func kf_setImageWithResource(resource: Resource?,
                     placeholderImage: Image? = nil,
                          optionsInfo: KingfisherOptionsInfo? = nil,
                        progressBlock: DownloadProgressBlock? = nil,
                    completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

Now:

```swift
func kf_setImage(with resource: Resource?,
                   placeholder: Image? = nil,
                       options: KingfisherOptionsInfo? = nil,
                 progressBlock: DownloadProgressBlock? = nil,
             completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

---

![][remove]

```swift
func kf_setImageWithURL(URL: NSURL?,
           placeholderImage: Image? = nil,
                optionsInfo: KingfisherOptionsInfo? = nil,
              progressBlock: DownloadProgressBlock? = nil,
          completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

> Now `URL` conforms to `Resource` protocol, so you could just use the `Resource` version above.

---

##### UIButton Extensions

![][modify]

Before:

```swift
func kf_setImageWithResource(resource: Resource?,
                       forState state: UIControlState,
                     placeholderImage: UIImage? = nil,
                          optionsInfo: KingfisherOptionsInfo? = nil,
                        progressBlock: DownloadProgressBlock? = nil,
                    completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

Now:

```swift
func kf_setImage(with resource: Resource?,
                     for state: UIControlState,
                   placeholder: UIImage? = nil,
                       options: KingfisherOptionsInfo? = nil,
                 progressBlock: DownloadProgressBlock? = nil,
             completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

---

![][modify]

Before:

```swift
func kf_setBackgroundImageWithResource(resource: Resource?,
                                 forState state: UIControlState,
                               placeholderImage: UIImage? = nil,
                                    optionsInfo: KingfisherOptionsInfo? = nil,
                                  progressBlock: DownloadProgressBlock? = nil,
                              completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

Now:

```swift
kf_setBackgroundImage(with resource: Resource?,
                          for state: UIControlState,
                        placeholder: UIImage? = nil,
                            options: KingfisherOptionsInfo? = nil,
                      progressBlock: DownloadProgressBlock? = nil,
                  completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

---

![][remove]

```swift
kf_setImageWithURL(URL: NSURL?,
        forState state: UIControlState,
      placeholderImage: UIImage? = nil,
           optionsInfo: KingfisherOptionsInfo? = nil,
         progressBlock: DownloadProgressBlock? = nil,
     completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

> Use the `Resource` version above.

---

![][remove]

```swift
kf_setBackgroundImageWithURL(URL: NSURL?,
                  forState state: UIControlState,
                placeholderImage: UIImage? = nil,
                     optionsInfo: KingfisherOptionsInfo? = nil,
                   progressBlock: DownloadProgressBlock? = nil,
               completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

> Use the `Resource` version above.

---

##### NSButton Extensions

![][modify]

Before:

```swift
func kf_setImageWithResource(resource: Resource?,
                     placeholderImage: Image? = nil,
                          optionsInfo: KingfisherOptionsInfo? = nil,
                        progressBlock: DownloadProgressBlock? = nil,
                    completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

Now: 

```swift
func kf_setImage(with resource: Resource?,
                   placeholder: Image? = nil,
                       options: KingfisherOptionsInfo? = nil,
                 progressBlock: DownloadProgressBlock? = nil,
             completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

---

![][modify]

Before:

```swift
kf_setAlternateImageWithResource(resource: Resource?,
                         placeholderImage: Image? = nil,
                              optionsInfo: KingfisherOptionsInfo? = nil,
                            progressBlock: DownloadProgressBlock? = nil,
                        completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

Now: 

```swift
func kf_setAlternateImage(with resource: Resource?,
                            placeholder: Image? = nil,
                                options: KingfisherOptionsInfo? = nil,
                          progressBlock: DownloadProgressBlock? = nil,
                      completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

---

![][remove]

```swift
func kf_setImageWithURL(URL: NSURL?,
           placeholderImage: Image? = nil,
                optionsInfo: KingfisherOptionsInfo? = nil,
              progressBlock: DownloadProgressBlock? = nil,
          completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

> Use the `Resource` version above.

---

![][remove]

```swift
func kf_setAlternateImageWithURL(URL: NSURL?,
                    placeholderImage: Image? = nil,
                         optionsInfo: KingfisherOptionsInfo? = nil,
                       progressBlock: DownloadProgressBlock? = nil,
                   completionHandler: CompletionHandler? = nil) -> RetrieveImageTask
```

> Use the `Resource` version above.

---

#### Image Extensions

![][modify]

Before:

```swift
func kf_normalizedImage() -> Image
```

Now:

```swift
func kf_normalized() -> Image
```

---

![][add]

```swift
func kf_image(withRoundRadius radius: CGFloat, fit size: CGSize, scale: CGFloat) -> Image
```

---

![][add]

```swift
func kf_resize(to size: CGSize) -> Image
```

---

![][add]

```swift
func kf_blurred(withRadius radius: CGFloat) -> Image
```

---

![][add]

```swift
func kf_overlaying(with color: Color, fraction: CGFloat) -> Image
```

---

![][add]

```swift
func kf_tinted(with color: Color) -> Image
```

---

![][add]

```swift
func kf_adjusted(brightness: CGFloat, 
                   contrast: CGFloat, 
                 saturation: CGFloat, 
                    inputEV: CGFloat) -> Image
```

![][add]

```swift
func kf_apply(_ filter: Filter) -> Image
```

---

#### KingfisherManager

![][modify]

Before:

```swift
class var sharedManager: KingfisherManager { get }
```

Now:

```swift
static let shared: KingfisherManager
```

---

![][remove]

```swift
init()
```

> You should not create a `KingfisherManager` instance yourself. Use `shared` instead.

---

![][modify]

Before:

```swift
func retrieveImageWithResource(resource: Resource,
                            optionsInfo: KingfisherOptionsInfo?,
                          progressBlock: DownloadProgressBlock?,
                      completionHandler: CompletionHandler?) -> RetrieveImageTask
```

Now: 

```swift
func retrieveImage(with resource: Resource,
                         options: KingfisherOptionsInfo?,
                   progressBlock: DownloadProgressBlock?,
               completionHandler: CompletionHandler?) -> RetrieveImageTask
```

---

![][remove]

```swift
func retrieveImageWithURL(URL: NSURL,
                  optionsInfo: KingfisherOptionsInfo?,
                progressBlock: DownloadProgressBlock?,
            completionHandler: CompletionHandler?) -> RetrieveImageTask
```

> Use the `Resource` version above.

---

#### ImageCache

![][modify]

Before:

```swift
let KingfisherDidCleanDiskCacheNotification: String
```

Now:

```swift
public extension Notification.Name {
    static var KingfisherDidCleanDiskCache: Notification.Name { get }
}
```

---

![][modify]

Before:

```swift
class var defaultCache: ImageCache { get }
```

Now:

```swift
static let `default`: ImageCache
```

---

![][modify]

Before:

```swift
func storeImage(image: Image, 
                originalData: NSData? = nil, 
                forKey key: String, 
                toDisk: Bool = true, 
                completionHandler: (() -> Void)? = nil)
```

Now:

```swift
func store(_ image: Image,
           original: Data? = nil,
           forKey key: String,
           processorIdentifier identifier: String = "",
           cacheSerializer serializer: CacheSerializer = DefaultCacheSerializer.default,
           toDisk: Bool = true,
           completionHandler: (() -> Void)? = nil)
```

> Leave `identifier` and `serializer` their default values while upgrading. They are used with `ImageProcessor` and `CacheSerializer` to customize the image decoding and serialization.

---

![][modify]

Before:

```swift
func removeImageForKey(key: String, 
                       fromDisk: Bool = true, 
                       completionHandler: (() -> Void)? = nil)
```

Now:

```swift
func removeImage(forKey key: String,
                 processorIdentifier identifier: String = "",
                 fromDisk: Bool = true,
                 completionHandler: (() -> Void)? = nil)
```

> Leave `identifier` its default values while upgrading. It is used with `ImageProcessor` to customize the image decoding.

---

![][modify]

Before:

```swift
func retrieveImageForKey(key: String, 
                     options: KingfisherOptionsInfo?, 
           completionHandler: ((Image?, CacheType) -> ())?) -> RetrieveImageDiskTask?
```

Now:

```swift
func retrieveImage(forKey key: String,
                      options: KingfisherOptionsInfo?,
            completionHandler: ((Image?, CacheType) -> ())?) -> RetrieveImageDiskTask?
```

---

![][modify]

Before:

```swift
func retrieveImageInMemoryCacheForKey(key: String) -> Image?
```

Now:

```swift
func retrieveImageInMemoryCache(forKey key: String, 
                                   options: KingfisherOptionsInfo? = nil) -> Image?
```

---

![][modify]

Before:

```swift
func retrieveImageInDiskCacheForKey(key: String, 
                                  scale: CGFloat = 1.0, 
                      preloadAllGIFData: Bool = false) -> Image?
```

Now:

```swift
func retrieveImageInDiskCache(forKey key: String, 
                                 options: KingfisherOptionsInfo? = nil) -> Image?
```

> Wrap the `scale` and `preloadAllGIFData` into `KingfisherOptionsInfo` if you were using them.

---

![][modify]

Before:

```swift
func clearDiskCacheWithCompletionHandler(completionHander: (()->())?)
```

Now:

```swift
func clearDiskCache(completion handler: (()->())? = nil)
```

---

![][modify]

Before:

```swift
func cleanExpiredDiskCacheWithCompletionHander(completionHandler: (()->())?)
```

Now:

```swift
func cleanExpiredDiskCache(completion handler: (()->())? = nil)
```

---

![][remove]

```swift
func cachedImageExistsforURL(url: NSURL) -> Bool
```

> Use result of `isImageCached(forKey:processorIdentifier:)` instead.

---

![][modify]

Before:

```swift
func isImageCachedForKey(key: String) -> CacheCheckResult
```

Now:

```swift
func isImageCached(forKey key: String, 
                   processorIdentifier identifier: String = "") -> CacheCheckResult
```

---

![][modify]

Before:

```swift
func hashForKey(key: String) -> String
```

Now:

```swift
func hash(forKey key: String, 
          processorIdentifier identifier: String = "") -> String
```

---

![][modify]

Before:

```swift
func calculateDiskCacheSizeWithCompletionHandler(completionHandler: ((size: UInt) -> ()))
```

Now:

```swift
func calculateDiskCacheSize(completion handler: ((_ size: UInt) -> ()))
```

---

![][modify]

Before: 

```swift
func cachePathForKey(key: String) -> String
```

Now:

```swift
func cachePath(forKey key: String, 
               processorIdentifier identifier: String = "") -> String
```

#### ImageDownloader

![][remove]

```swift
var requestModifier: ((inout URLRequest) -> Void)?
```

> Use `.requestModifier` in `KingfisherOptionsInfo` instead. See [Modify a request before sending](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#modify-a-request-before-sending) for mroe.

---

![][modify]

Before:

```swift
class var defaultDownloader: ImageDownloader { get }
```

Now:

```swift
static let `default`: ImageDownloader
```

---

![][modify]

Before:

```swift
func downloadImageWithURL(URL: NSURL,
                      options: KingfisherOptionsInfo?,
                progressBlock: ImageDownloaderProgressBlock?,
            completionHandler: ImageDownloaderCompletionHandler?) -> RetrieveImageDownloadTask?
```

Now:

```swift
func downloadImage(with url: URL,
                    options: KingfisherOptionsInfo? = nil,
              progressBlock: ImageDownloaderProgressBlock? = nil,
          completionHandler: ImageDownloaderCompletionHandler? = nil) -> RetrieveImageDownloadTask?
```

---



##### RetrieveImageDownloadTask

![][modify]

Before:

```swift
var URL: NSURL? { get }
```

Now:

```swift
var url: URL? { get }
```

---

##### ImageDownloaderDelegate

![][modify]

Before:

```swift
func imageDownloader(downloader: ImageDownloader, 
         didDownloadImage image: Image, 
                     forURL URL: NSURL, 
          withResponse response: NSURLResponse)
```

Now:

```swift
imageDownloader(_ downloader: ImageDownloader, 
           didDownload image: Image, 
                     for url: URL, 
               with response: URLResponse?)
```

---

![][add]

```swift
func isValidStatusCode(_ code: Int, for downloader: ImageDownloader) -> Bool
```

> Use this delegate method to specify valid HTTP status code for a request.

---

##### AuthenticationChallengeResponable

![][modify]

Before:

```swift
func downloader(downloader: ImageDownloader, 
                didReceiveChallenge challenge: NSURLAuthenticationChallenge, 
                completionHandler: (NSURLSessionAuthChallengeDisposition, NSURLCredential?) -> Void)
```

Now:

```swift
func downloader(_ downloader: ImageDownloader, 
                didReceive challenge: URLAuthenticationChallenge, 
                completionHandler: @escaping (URLSession.AuthChallengeDisposition, URLCredential?) -> Void)
```

---

#### ImagePrefetcher

![][modify]

Before:

```swift
init(urls: [NSURL], 
     optionsInfo: KingfisherOptionsInfo? = nil,
     progressBlock: PrefetcherProgressBlock? = nil,
     completionHandler: PrefetcherCompletionHandler? = nil)
```

Now:

```swift
init(urls: [URL], 
     options: KingfisherOptionsInfo? = nil,
     progressBlock: PrefetcherProgressBlock? = nil,
     completionHandler: PrefetcherCompletionHandler? = nil)
```

> The `URL` version of init method is kept since there is no covariance in Swift protocol.

---

![][modify]

Before:

```swift
init(resources: [Resource], 
     optionsInfo: KingfisherOptionsInfo? = nil,
     progressBlock: PrefetcherProgressBlock? = nil,
     completionHandler: PrefetcherCompletionHandler? = nil)
```

Now:

```swift
init(resources: [Resource],
     options: KingfisherOptionsInfo? = nil,
     progressBlock: PrefetcherProgressBlock? = nil,
     completionHandler: PrefetcherCompletionHandler? = nil)
```

#### ImageTransition

Enum members now follow lowercase naming convention. See "[Enum Member](#enum-member)" part of this documentation.

#### KingfisherOptionsInfo

Enum members now follow lowercase naming convention. See "[Enum Member](#enum-member)" part of this documentation.

---

![][modify]

Before:

```swift
case TargetCache(ImageCache?)
```

Now:

```swift
case targetCache(ImageCache)
```

> Only accept non-optional value now. Use `ImageCache.default` if you were passing a `nil` before.

---

![][modify]

Before:

```swift
case Downloader(ImageDownloader?)
```

Now:

```swift
case downloader(ImageDownloader)
```

> Only accept non-optional value now. Use `ImageDownloader.default` if you were passing a `nil` before.

---

![][add]

```swift
case requestModifier(ImageDownloadRequestModifier)
```

> Use this option to pass a request modifier instead of setting the `requestModifier` property in `ImageDownloader`.

---

![][add]

```swift
case processor(ImageProcessor)
```

---

![][add]

```swift
case cacheSerializer(CacheSerializer)
```

#### ImageProcessor

![][add]

```swift
public enum ImageProcessItem { ... }
```

---

![][add]

```swift
protocol ImageProcessor { ... }
```

> See [Processor](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#processor) part in Cheat Sheet to know more about the processor.

---

![][add]

```swift
struct DefaultImageProcessor: ImageProcessor { ... }
struct RoundCornerImageProcessor: ImageProcessor { ... }
struct ResizingImageProcessor: ImageProcessor { ... }
struct BlurImageProcessor: ImageProcessor { ... }
struct OverlayImageProcessor: ImageProcessor { ... } 
struct TintImageProcessor: ImageProcessor { ... }
struct ColorControlsProcessor: ImageProcessor { ... } 
struct BlackWhiteProcessor: ImageProcessor { ... }
```

> These are built-in processor of Kingfisher. You could easily create your own processor to decode/filter any data/image to final image. See [Create and use your own processor](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#create-and-use-your-own-processor) for more.

---

#### CacheSerializer

![][add]

```swift
protocol CacheSerializer { ... }
```

> See [Serializer](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#serializer) part in Cheat Sheet to know more about the cache serializer.

---

![][add]

```swift
struct DefaultCacheSerializer: CacheSerializer { ... }
```

> Default serializer to serialize/deserialize basic image format: PNG, JEPG and GIF. See [Create and use your own serializer](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#create-and-use-your-own-serializer) to know how to create and use a serializer yourself.

#### ImageDownloadRequestModifier

![][add]

```swift
protocol ImageDownloadRequestModifier { ... }
```

---

![add]

```swift
struct AnyModifier: ImageDownloadRequestModifier { ... }
```

> A block-based modifier which supply an easy way to implement a modifier. See [Modify a request before sending](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#modify-a-request-before-sending).


[add]: https://img.shields.io/badge/api-add-green.svg
[modify]: https://img.shields.io/badge/api-modify-yellow.svg
[remove]: https://img.shields.io/badge/api-remove-red.svg


