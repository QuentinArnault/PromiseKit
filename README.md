![PromiseKit](http://promisekit.org/public/img/logo-tight.png)

![badge-pod] ![badge-languages] ![badge-pms] ![badge-platforms] [![Build Status](https://travis-ci.org/mxcl/PromiseKit.svg?branch=master)](https://travis-ci.org/mxcl/PromiseKit)

[繁體中文](README.zh_Hant.md) (*outdated*), [简体中文](README.zh_CN.md) (*outdated*)

---

Promises simplify asynchronous programming, freeing you up to focus on the more
important things. They are easy to learn, easy to master and result in clearer,
more readable code. Your co-workers will thank you.

```swift
UIApplication.shared.isNetworkActivityIndicatorVisible = true

let fetchImage = URLSession.shared.dataTask(.promise, with: url).flatMap{ UIImage(data: $0.data) }
let fetchLocation = CLLocationManager.requestLocation()

firstly {
    when(fulfilled: fetchImage, fetchLocation)
}.done { image, location in
    self.imageView.image = image
    self.label.text = "\(location)"
}.ensure {
    UIApplication.shared.isNetworkActivityIndicatorVisible = false
}.catch { error in
    self.show(UIAlertController(for: error), sender: self)
}
```

PromiseKit is a thoughtful and complete implementation of promises for any
platform with a `swiftc`, it has *excellent* Objective-C bridging and
*delightful* specializations for iOS, macOS, tvOS and watchOS. It is a top-100
pod used in many of the most popular apps in the world.

# PromiseKit 6 Released

PromiseKit 6 has been released; [read the release notes and migration guide][PMK6].

# Quick Start

In your [Podfile]:

```ruby
use_frameworks!

target "Change Me!" do
  pod "PromiseKit", "~> 6.0"
end
```

PromiseKit 6, 5 and 4 support Xcode 8.3, 9.0, 9.1 and 9.2; Swift 3.1,
3.2, 3.3, 4.0 and 4.1 ; iOS, macOS, tvOS, watchOS, Linux and Android; CocoaPods,
Carthage and SwiftPM; ([CI Matrix](https://travis-ci.org/mxcl/PromiseKit)).

For Carthage, SwiftPM, etc., or for instructions when using older Swifts or
Xcodes see our [Installation Guide](Documentation/Installation.md).

# Documentation

* Handbook
  * [Getting Started](Documentation/GettingStarted.md)
  * [Promises: Common Patterns](Documentation/CommonPatterns.md)
  * [Frequently Asked Questions](Documentation/FAQ.md)
* Manual
  * [Installation Guide](Documentation/Installation.md)
  * [Objective-C Guide](Documentation/ObjectiveC.md)
  * [Troubleshooting](Documentation/Troubleshooting.md) (eg. solutions to common compile errors)
  * [Appendix](Documentation/Appendix.md)

If you are looking for a function’s documentation, then please note
[our sources](Sources/) are thoroughly documented.

# Extensions

Promises are only as useful as the asynchronous tasks they represent, thus we
have converted (almost) all of Apple’s APIs to promises. The default CocoaPod
provides Promises and the extensions for Foundation and UIKit. The other
extensions are available by specifying additional subspecs in your `Podfile`,
eg:

```ruby
pod "PromiseKit/MapKit"          # MKDirections().calculate().then { /*…*/ }
pod "PromiseKit/CoreLocation"    # CLLocationManager.requestLocation().then { /*…*/ }
```

All our extensions are separate repositories at the [PromiseKit organization].

## I don't want the extensions!

Then don’t have them:

```ruby
pod "PromiseKit/CorePromise", "~> 6.0"
```

> *Note* Carthage installations come with no extensions by default.

## Choose Your Networking Library

Promise chains are commonly started with networking, thus we offer multiple
options: [Alamofire], [OMGHTTPURLRQ] and of course (vanilla) `NSURLSession`:

```swift
// pod 'PromiseKit/Alamofire'
// https://github.com/PromiseKit/Alamofire
firstly {
    Alamofire.request("http://example.com", method: .post, parameters: params).responseJSON()
}.done { rsp in
    // `rsp.json`
}.catch { error in
    //…
}

// pod 'PromiseKit/OMGHTTPURLRQ'
// https://github.com/PromiseKit/OMGHTTPURLRQ
firstly {
    URLSession.POST("http://example.com", JSON: params)
}.flatMap {
    try JSONDecoder().decoder(Foo.self, with: $0.data)
}.done { foo in
    //…
}.catch { error in
    //…
}

// pod 'PromiseKit/Foundation'
// https://github.com/PromiseKit/Foundation
firstly {
    URLSession.shared.dataTask(.promise, with: try makeRequest())
}.flatMap {
    try JSONDecoder().decode(Foo.self, with: $0.data)
}.done { foo in
    //…
}.catch { error in
    //…
}

func makeRequest() throws -> URLRequest {
    var rq = URLRequest(url: url)
    rq.httpMethod = "POST"
    rq.addValue("application/json", forHTTPHeaderField: "Content-Type")
    rq.addValue("application/json", forHTTPHeaderField: "Accept")
    rq.httpBody = try JSONSerialization.jsonData(with: obj)
    return rq
}
```

Nowadays, considering that:

* We almost always POST JSON
* We now have `JSONDecoder`
* PromiseKit now has `flatMap`

We recommend vanilla `URLSession`; use less black-boxes, stick closer to the
metal. Alamofire was essential until the three bulletpoints above became true,
but nowadays it isn’t really necessary. OMGHTTPURLRQ was developed before JSON
was the modern standard and thus REST requests were hard, but nowadays you
rarely network anything but JSON.

# Support

Ask your question at our [Gitter chat channel] or on [our bug tracker].


[badge-pod]: https://img.shields.io/cocoapods/v/PromiseKit.svg?label=version
[badge-pms]: https://img.shields.io/badge/supports-CocoaPods%20%7C%20Carthage%20%7C%20SwiftPM-green.svg
[badge-languages]: https://img.shields.io/badge/languages-Swift%20%7C%20ObjC-orange.svg
[badge-platforms]: https://img.shields.io/badge/platforms-macOS%20%7C%20iOS%20%7C%20watchOS%20%7C%20tvOS%20%7C%20Linux-lightgrey.svg
[badge-mit]: https://img.shields.io/badge/license-MIT-blue.svg
[OMGHTTPURLRQ]: https://github.com/mxcl/OMGHTTPURLRQ
[Alamofire]: http://alamofire.org
[PromiseKit organization]: https://github.com/PromiseKit
[Gitter chat channel]: https://gitter.im/mxcl/PromiseKit
[our bug tracker]: https://github.com/mxcl/PromiseKit/issues/new
[Podfile]: https://guides.cocoapods.org/syntax/podfile.html
[PMK6]: http://promisekit.org/news/2018/02/PromiseKit-6.0-Released/