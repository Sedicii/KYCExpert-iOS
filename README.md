# KYCExpert iOS SDK

KYC manages compliance with country and product specific rules 
(AML/CTF, FINRA, EMIR, and 5th EU AMLD) to efficiently onboard
and maintain multi-product and multi-jurisdictional clients in retail,
wealth management, commercial and institutional banking.
        
## Full example
A full example which integrates Kagii SDK with KYCExpert SDK is available in the repository
[Sedicii/KYCexpert-High-Assurance-Example](https://github.com/Sedicii/KYCexpert-High-Assurance-Example)

## Requirements

* iOS 13.0 and higher
* Permissions for camera, microphone
* Internet connection

## Permissions

KYCExpert SDK requires permission to access the camera and microphone.

According to the Apple documentation, an iOS app linked on or after iOS 10.0, “must statically
declare the intent to do so”. It is necessary to include the **NSCameraUsageDescription**, **NSPhotoLibraryUsageDescription**
and **NSMicrophoneUsageDescription** keys in your app’s Info.plist file and provide a purpose string for this key.

If your app attempts to access any of the device’s without a corresponding purpose string, your app exits.


## Include SDK in Project
This framework can be included in your project with [__Carthage__](https://github.com/Carthage/Carthage):

```
github "Sedicii/KYCExpert-iOS"
```

Or you can include it manually downloading
[__the latest version__](https://github.com/Sedicii/KYCExpert-iOS/releases/latest).

## SDK Configuration

The KYC Expert SDK has only one method to start or continue the KYC process,
to make it as simpler as we can.

These are the available variables to configure the SDK:

| Name                    | Required  | Description                                                                   |
|-------------------------|-----------|-------------------------------------------------------------------------------|
| API_ENDPOINT            |   true    | The API_ENDPOINT of the backend used by the SDK, Sedicii will provide it.     |
| API_KEY                 |   true    | The API_KEY of the backend used by the SDK, Sedicii will provide it.          |
| REDIRECT_URI            |   true    | The REDIRECT_URI of the authorised domain, Sedicii will provide it.           |

To configure the SDK in iOS a plist type file is needed located in the main project path, with name KYCExpert-Info.plist 
(make sure it is added in BuildPhases > Copy Bundle Resources), which can have the following values:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>API_ENDPOINT</key>
	<string>https://example.kyc.sedicii.com</string>
	<key>API_KEY</key>
	<string>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</string>
	<key>REDIRECT_URI</key>
	<string>https://example.kyc.sedicii.com/admin/login</string>
</dict>
</plist>
```

### Start / Continue KYC

Start/continue method requires the authorisation code from [Kagii](https://github.com/Sedicii/Kagii-iOS).

Once finished the KYC process the SDK will give control back to the application using the native approach of each platform.

```swift
KYCExpert.startKYC(code: <Kagii response code>) { kycResult in
    switch kycResult {
    case .failure(let error):
        // error - KYCExpertError
    case .success(let status):
        // status - KYCExpertStatus
    }
}
```

#### KYCExpertError
```swift
public enum KYCExpertError: Error {
    case error(String)
}
```

#### KYCExpertStatus
```swift
public enum KYCExpertStatus: String {
    case started
    case ongoing
    case finished

    public var description: String {
        switch self {
        case .started:
            return L10n.sdkProcessStarted
        case .ongoing:
            return L10n.sdkProcessOngoing
        case .finished:
            return L10n.sdkProcessFinished
        }
    }
}
```

### Integration with Kagii

```swift
Kagii.userInterface.logInUser { kagiiResult in
    switch kagiiResult {
    case .failure(let error):
        // TODO - your code
    case .success(let response):
        // In the success case your send response.code to KYCExpert SDK
        KYCExpert.startKYC(code: response.code) { kycResult in
            switch kycResult {
            case .failure(let error):
                // TODO - your code
            case .success(let status):
                // TODO - your code
            }
        }
    }
}
```


### Appearance

The full appearance definition is available in [appearance section](/docs/appearance.md).

Defining the appearance. This is way to define your own appearance.

```swift
KYCExpert.appearance = ClientAppearance.appearance
```
