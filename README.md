## SaltEdge iOS

A handful of classes to help you interact with the Salt Edge API from your iOS app.

## Requirements

iOS 7+, ARC.

## Installation
### CocoaPods

TBD.

### Source

Clone this repository

`$ git clone git@github.com:saltedge/saltbridge-ios.git`

Copy the `Salt Edge API` folder into your project.

## SBWebView

A small `UIWebView` replacement for using [Salt Edge Connect](https://docs.saltedge.com/guides/connect/) within your iOS app.

### Usage

* Import the class and delegate files into your view controller
* Add a `SBWebView` instance to your view controllers' view, also set a `stageDelegate` for the web view
* Implement the `SBWebViewDelegate` methods in delegates' class
* Load the connect page in the web view

**NOTE:** Do not use the `delegate` property on `SKWebView`, since an `SKWebView` acts like a proxy object. If your class does need to respond to the `UIWebView` delegate methods, just implement them and the `SKWebView` instance will forward those messages to its `stageDelegate`.

### Example

Import the class and delegate files into your view controller, also let your view controller conform to the `SBWebViewDelegate` protocol.

```objc
#import "SBWebView.h"
#import "SBWebViewDelegate.h"
// ... snip ...

@interface MyViewController() <SKWebViewDelegate>
// ... snip ...
```

Instantiate a `SBWebView` and add it to your controller:

```objc
SKWebView* connectWebView = [[SBWebView alloc] initWithFrame:self.view.frame stateDelegate:self];
```

Implement the `SBWebViewDelegate` methods in your controller:

```objc
// ... snip ...

- (void)webView:(SBWebView *)webView receivedCallbackWithResponse:(NSDictionary *)response
{
    NSNumber* loginID    = response[SBLoginDataKey][SBLoginIdKey];
    NSString* loginState = response[SBLoginDataKey][SBLoginStateKey];
    // do something with the data...
}

- (void)webView:(SBWebView *)webView receivedCallbackWithError:(NSError *)error
{
  // handle the error...
}
```

Keep in mind that you can also implement the `UIWebView` delegate methods:

```objc
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
  // the method will be called after SKWebView has finished processing it
}
```

Load the Salt Edge Connect URL into the web view and you're good to go:

```objc
[connectWebView loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:connectURLString]]];
```

## SEAPIRequestManager

An `AFHTTPSessionManager` subclass, designed with convenience methods for interacting with and querying the Salt Edge API. Contains methods for fetching entities (logins, transactions, et al.), also for creating a login via the REST API. In addition, if you're using the `SBWebView` to create or reconnect logins, this class provides a method for requesting a Connect token as well.

### Usage

Import the manager class and link your app id and app secret in the first place before using it.

### Example

```objc
#import "SEAPIRequestManager.h"

// snip...
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [SEAPIRequestManager linkAppId:kAppId appSecret:kAppSecret];
    // snip...
}
```

Use the manager to interact with the provided API:

```objc
- (void)requestConnectToken
{
    SEAPIRequestManager* manager = [SEAPIRequestManager manager];

    [manager requestConnectTokenWithParameters:@{ @"customer_email" : @"user@example.com" } success:^(NSURLSessionDataTask* task, NSDictionary* tokenDictionary) {
        NSString* connectURL = tokenDictionary[kConnectURLKey];
        // load the connect URL into the SBWebView...
    } failure:^(NSURLSessionDataTask* task, NSError* error) {
        // something's not right..
    }];
}
```

## Models

There are some provided models for serializing the objects received in the API responses. These represent the providers, logins, accounts, transactions, provider fields and their options. Whenever you request a resource that returns one of these types, they will always get serialized into Objective-C classes. (For instance, the `fetchFullLoginsListWithSuccess:failure:` method has a `NSSet` containing `SELogin` instances in it's success callback.)

Models contained within the components:

* `SEAccount`
* `SELogin`
* `SEProvider`
* `SEProviderField`
* `SEProviderFieldOption`
* `SETransaction`

For a supplementary description of the models listed above that is not included in the sources' docs, feel free to visit the [API Reference](https://docs.saltedge.com/reference/).

## Utilities and categories

A few categories and utility classes are bundled within the components, and are used internally, but you could also use them if you find that necessary.

## Documentation

Documentation is available for all of the components. Use quick documentation (Alt+Click) to get a quick glance at the documentation for a method or a property.

## License

See the LICENSE file.

## References

1. [Salt Edge Connect Guide](https://docs.saltedge.com/guides/connect/)
2. [Salt Edge API Reference](https://docs.saltedge.com/reference/)
