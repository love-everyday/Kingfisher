![](https://raw.githubusercontent.com/onevcat/Kingfisher/master/images/logo.png)

Kingfisher is a lightweight and pure Swift implemented library for downloading and caching image from the web. This project is heavily inspired by the popular [SDWebImage](https://github.com/rs/SDWebImage). And it provides you a chance to use pure Swift alternative in your next app.

## Features

- [x] Asynchronous image downloading and caching.
- [x] `URLSession` based networking. Basic image processors and filters supplied.
- [x] Multiple-layer cache for both memory and disk.
- [x] Cancelable downloading and processing task to improve performance.
- [x] Independent components. Use the downloader or caching system separately as you need.
- [x] Prefetching images and show them from cache later when necessary.
- [x] Extension over `UIImageView`, `NSImage` and `UIButton` for setting image from a URL directly.
- [x] Built-in transition animation when setting images.
- [x] Extendable image processing and more image format support.

The simplest using case is setting an image to an image view with extension:

```swift
let url = URL(string: "url_of_your_image")
imageView.kf_setImage(with: url)
```

It will download the image from `url`, send it to both memory and disk cache, then show it in the `imageView`. When you use the same code later, the image will be retrieved from cache and show immediately.

## Requirements

- iOS 8.0+ / macOS 10.10+ / tvOS 9.0+ / watchOS 2.0+
- Swift 3 (Kingfisher 3.x), Swift 2.3 (Kingfisher 2.5.x)

## Next

Follow the [Installation Guide](https://github.com/onevcat/Kingfisher/wiki/Install-Kingfisher) to integrate Kingfisher to your project.

Curious about what Kingfisher could do and how would it look like when used in your project? See our [Cheat Sheet](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet) page, in which some useful code snippet are listed.

We also introduced the [Concept and Modules](https://github.com/onevcat/Kingfisher/wiki/Concept-and-Modules) of Kingfisher in depth to help you understand how does this framework work. At last, also remember to check the full [API Reference](http://cocoadocs.org/docsets/Kingfisher/) whenever you need to know more about Kingfisher.

## Other

### Future of Kingfisher

I want to keep Kingfisher slim. This framework will focus on providing a simple solution for image downloading and caching. But that does not mean the framework will not be improved. Kingfisher is far away from perfect, and necessary and useful features will be added later to make it better.

### About the logo

The logo of Kingfisher is inspired by [Tangram (七巧板)](http://en.wikipedia.org/wiki/Tangram), a dissection puzzle consisting of seven flat shapes from China. I believe she's a kingfisher bird instead of a swift, but someone insists that she is a pigeon. I guess I should give her a name. Hi, guys, do you have any suggestion?

### Contact

Follow and contact me on [Twitter](http://twitter.com/onevcat) or [Sina Weibo](http://weibo.com/onevcat). If you find an issue, just [open a ticket](https://github.com/onevcat/Kingfisher/issues/new) on it. Pull requests are warmly welcome as well.

### License

Kingfisher is released under the MIT license. See [LICENSE](https://github.com/onevcat/Kingfisher/blob/master/LICENSE) for details.


