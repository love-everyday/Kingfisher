#### I cannot download an image.

Please check whether you are trying to access with an image resource with "http" scheme. If so, you need to switch to "https" or [disable ATS](http://iosdevtips.co/post/121756573323/ios-9-xcode-7-http-connect-server-error) in your Info.plist.

#### The image cannot be cached and everytime I have to download it again.

Kingfisher is using a hashed the url absolute string for cache key by default. So if your url changes, even only the query part or timestamp, Kingfisher will think that it is a new image which is not cached yet. 

#### Can I load some special image format like WebP?

Yes, but it is not built-in supported in Kingfisher. You may need to implement your own [`ImageProcessor`](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#create-and-use-your-own-processor) and [`CacheSerializer`](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#create-and-use-your-own-serializer)

#### Does Kingfisher supports authentication with server?

Yes. You could setup an `ImageDownloadRequestModifier` and pass it in option to modify the request header. It is useful when you are doing a basic HTTP authentication or any token based authentication. If you are using some certification, please set `authenticationChallengeResponder` of `ImageDownloader` and [implement your auth logic](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet#authentication-with-nsurlcredential) in the delegate method.

#### Can I just download or get the image without setting to the image view?

Sometimes you are not doing UI things and just want the image. You could use `KingfisherManager` for it:

```swift
KingfisherManager.shared.retrieveImage(with: url) { 
    (image, error, cacheType, url) in
    if let image = image {
        // Use image
    }
}
``` 

This will check the cache first, if the image is not cached yet, it will download it from given url. If you need more control of it, use `ImageDownloader` and `ImageCache` instead.


