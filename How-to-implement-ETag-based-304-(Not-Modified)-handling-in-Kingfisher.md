
> This wiki page is wrote on 30 Apr, 2015, for Kingfisher 1.3.0. I hope I can remember to update it if something changes later.

## What is the problem?

### 304 Not Modified

If the client has performed a conditional GET request and access is allowed, but the document has not been modified, the server should respond with this status code. The 304 response must not contain a message-body, and thus is always terminated by the first empty line after the header fields.

### Kingfisher cache system

Sometimes, you will want to get a new image with the same URL (different avatar for the same user, for example). But since the cache system of Kingfisher is based on URL, it will prefer to use the cache by default. By using the option of `KingfisherOptions.ForceRefresh`, you can bypass the cache system and force to download the image. But more often, it is not wise to download the image again if the same one is already cached. So we'd better to build a logic on the server to check if the requested image was modified. If not, the server should return a status code 304, and we can use the cached image instead of downloading it again.

This wiki will show how to handle 304 in Kingfisher.

## Implementation

Kingfisher will only handle the downloading and caching part. You need to implement your own logic to make the conditional GET request (client side). In this wiki, we assume that the server has an [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) based 304 checking and we will build the client part of it. 

We will obtain the ETags of downloaded images, store them somewhere, and send them to server if the same url request been made. Your server should check whether the image modified or not with this ETag and return a 304 if not modified. If Kingfisher gets a 304 response, it will respect the response and cached image will be used.

### Get Etag from response

First of all, we have to know the ETag of an image. By conforming `ImageDownloaderDelegate` we can extract the `ETag` entry from the response header and store it. For simplification, `NSUserDefault` is used. You should select a data structure fits your app best instead.

```swift
import Kingfisher

class MyViewController: UIViewController, ImageDownloaderDelegate {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        
        KingfisherManager.sharedManager.downloader.delegate = self
    }

    func imageDownloader(downloader: ImageDownloader,
             didDownloadImage image: UIImage,
                         forURL URL: NSURL,
              withResponse response: NSURLResponse)
    {
        if let httpResponse = response as? NSHTTPURLResponse,
               etag = httpResponse.allHeaderFields["ETag"] as? String,
               URLString = URL.absoluteString
        {
            let dictionaryKey = "image-etags"
            
            var dictionary = NSUserDefaults.standardUserDefaults().dictionaryForKey(dictionaryKey) as? [String: String]
            if dictionary == nil {
                dictionary = [String: String]()
            }
            
            let hash = KingfisherManager.sharedManager.cache.hashForKey(URLString)
            dictionary![hash] = etag
            NSUserDefaults.standardUserDefaults().setObject(dictionary, forKey: dictionaryKey)
            NSUserDefaults.standardUserDefaults().synchronize()
        }
    }

}
```

Here we store ETag with the hash of URL as the key. That is because Kingfisher is using the hash as the disk file name for anonymity. The stored ETag will be sent to server when the same URL is requested later. We will see it soon.

### Maintain Etags for cached images

We need to ensure the image is in the disk cache when sending the Etag to server. Since the disk cache has a expire duration and size limitation, there is a chance that some files are cleaned from the disk cache. You need to know which files are removed from disk, and remove the corresponding Etags as well. `KingfisherDidCleanDiskCacheNotification` would help.

Observe this notification in a place alive during the whole app life cycle (AppDelegate is a good example), and remove the Etags with the hash.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        // Override point for customization after application launch.
        
        NSNotificationCenter.defaultCenter().addObserver(self, selector: "diskImageCacheCleaned:", 
                name: KingfisherDidCleanDiskCacheNotification, object: KingfisherManager.sharedManager.cache)
        
        
        return true
    }

    func diskImageCacheCleaned(notification: NSNotification) {
        let dictionaryKey = "image-etags"

        let userDefaults = NSUserDefaults.standardUserDefaults()
        if let dictionary = userDefaults.dictionaryForKey(dictionaryKey) as? [String: String],
               hashes = notification.userInfo?[KingfisherDiskCacheCleanedHashKey] as? [String]
        {
            var result = dictionary
            for hash in hashes {
                result.removeValueForKey(hash)
            }
            
            userDefaults.setObject(result, forKey: dictionaryKey)
            userDefaults.synchronize()
        }
    }
}
```

Please notice that `KingfisherDidCleanDiskCacheNotification` will only be sent when cached files expired or the total size exceeding the max allowed size. If you clear the disk cache manually, you should remove all stored Etags yourself:

```swift
class SomeViewController : UIViewController {
    
    //...

    @IBAction func clearDiskCache(sender: AnyObject) {
        KingfisherManager.sharedManager.cache.clearDiskCache()

        NSUserDefaults.standardUserDefaults().removeObjectForKey("image-etags")
        NSUserDefaults.standardUserDefaults().synchronize()
    }

    //...
}
```

### Modify your request

At last, we will send the Etag along with the request in a "If-None-Match" field in the header. Use `requestModifier` to modify the request before sending it. We have to use `KingfisherOptions.ForceRefresh` as well to send the request first instead of searching in cache:

```swift    
KingfisherManager.sharedManager.downloader.requestModifier = {
    (request: NSMutableURLRequest) in
    
    if let URLString = request.URL?.absoluteString {
        let hash = KingfisherManager.sharedManager.cache.hashForKey(URLString)
        if let dictionary = NSUserDefaults.standardUserDefaults().dictionaryForKey("image-etags"),
               etag = dictionary[hash] as? String {
            request.setValue(etag, forHTTPHeaderField: "If-None-Match")
        }
    }
}

imageView.kf_setImageWithURL(NSURL(string: "your_url")!, 
            placeholderImage: nil, optionsInfo: [.Options: KingfisherOptions.ForceRefresh])
```

If the server returns a 304 Not Modified, Kingfisher will use the cached image. Otherwise, a new version is downloaded.

Remember to clean the `requestModifier` if you do not use it anymore, especially if you are referring `self` in it. It is a `strong` property!

I think that is all. Have fun.
