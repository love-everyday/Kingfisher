> #### Everything has a beginning. For using a framework, it's installation.

## Versions

Kingfisher supports Swift 3 from [version 3.0](https://github.com/onevcat/Kingfisher/releases/tag/3.0.0). The latest Kingfisher version compatible with Swift 2.3 and prior is version 2.6.x. See the [release page](https://github.com/onevcat/Kingfisher/releases) to find the version you need. We will use the latest version in this guide. But the installation process should be the same for all versions.

## Installation

### CocoaPods

[CocoaPods](http://cocoapods.org) is a dependency manager for Cocoa projects. You can install it with the following command:

``` bash
$ gem install cocoapods
```

To integrate Kingfisher into your Xcode project using CocoaPods, specify it to a target in your `Podfile`:

``` ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.0'
use_frameworks!

target 'MyApp' do
  # your other pod
  # ...
  pod 'Kingfisher', '~> 3.0'
end
```

Then, run the following command:

``` bash
$ pod install
```

You should open the `{Project}.xcworkspace` instead of the `{Project}.xcodeproj` after you installed anything from CocoaPods.

For more information about how to use CocoaPods, I suggest [this tutorial](http://www.raywenderlich.com/64546/introduction-to-cocoapods-2).

### Carthage

[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager for Cocoa application. To install the carthage tool, you can use [Homebrew](http://brew.sh).

``` bash
$ brew update
$ brew install carthage
```

To integrate Kingfisher into your Xcode project using Carthage, specify it in your `Cartfile`:

``` ogdl
github "onevcat/Kingfisher" ~> 3.0
```

Then, run the following command to build the Kingfisher framework:

``` bash
$ carthage update

```

At last, you need to set up your Xcode project manually to add the Kingfisher framework.

On your application targets’ “General” settings tab, in the “Linked Frameworks and Libraries” section, drag and drop each framework you want to use from the Carthage/Build folder on disk.

On your application targets’ “Build Phases” settings tab, click the “+” icon and choose “New Run Script Phase”. Create a Run Script with the following content:

``` 
/usr/local/bin/carthage copy-frameworks
```

and add the paths to the frameworks you want to use under “Input Files”:

``` 
$(SRCROOT)/Carthage/Build/iOS/Kingfisher.framework
```

For more information about how to use Carthage, please see its [project page](https://github.com/Carthage/Carthage).

### Swift Package Manager

[Swift Package Manager](https://github.com/apple/swift-package-manager) might be also your choice for package management. Just add the url of this repo to your `Package.swift` file as a dependency:

```swift
import PackageDescription

let package = Package(
    name: "YourAwesomeSoftware",
    dependencies: [
        .Package(url: "https://github.com/onevcat/Kingfisher.git",
                 majorVersion: 3)
    ]
)
```

Then run `swift build` whenever you get prepared.

You could know more information on how to use Swift Package Manager in Apple's [official page](https://swift.org/package-manager/).

### Manually

It is not recommended to install the framework manually, but if you prefer not to use either of the aforementioned dependency managers, you can integrate Kingfisher into your project manually. A regular way to use Kingfisher in your project would be using Embedded Framework.

- Add Kingfisher as a [submodule](http://git-scm.com/docs/git-submodule). In your favorite terminal, `cd` into your top-level project directory, and entering the following command:

``` bash
$ git submodule add https://github.com/onevcat/Kingfisher.git
```

- Open the `Kingfisher` folder, and drag `Kingfisher.xcodeproj` into the file navigator of your app project, under your app project.
- In Xcode, navigate to the target configuration window by clicking on the blue project icon, and selecting the application target under the "Targets" heading in the sidebar.
- In the tab bar at the top of that window, open the "Build Phases" panel.
- Expand the "Target Dependencies" group, and add `Kingfisher.framework`.
- Click on the `+` button at the top left of "Build Phases" panel and select "New Copy Files Phase". Rename this new phase to "Copy Frameworks", set the "Destination" to "Frameworks", and add `Kingfisher.framework` of the platform you need.

## Next

After installation, you could import Kingfisher to your project by adding this:

```swift
import Kingfisher
```

to the files in which you want to use this framework.

Once you prepared, continue to have a look at the [Cheat Sheet](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet) to see how to use Kingfisher.

