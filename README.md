# Capabilities

The objective of your chapter is to write a data-driven/research-based answer/blog post to this big question: “What is the state of web capabilities in 2020?”

HTTPS, Security and Privacy
Progressive Enhancement / feature Detection

https://github.com/HTTPArchive/almanac.httparchive.org/wiki/Authors'-Guide#writing-the-chapter

https://developers.google.com/style/highlights


<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Introduction](#introduction)
- [Web Share API](#web-share-api)
- [Sharing Text and URLs](#sharing-text-and-urls)
- [Sharing Files](#sharing-files)
- [Clipboard Access](#clipboard-access)
  - [Read Access](#read-access)
  - [Write Access](#write-access)
- [File System Access](#file-system-access)
- [Contact Picker](#contact-picker)
- [Storage](#storage)
  - [Estimate the available storage](#estimate-the-available-storage)
  - [Opt-in to persistant storage](#opt-in-to-persistant-storage)
- [Notifications](#notifications)
  - [Badging](#badging)
  - [Scheduling Notifications](#scheduling-notifications)
- [Wake Lock API](#wake-lock-api)
- [Idle Detection API](#idle-detection-api)
- [Web OTP](#web-otp)
- [Text Fragment](#text-fragment)
- [Periodic Background Sync](#periodic-background-sync)
- [Integration with native app stores](#integration-with-native-app-stores)
- [Devices](#devices)
- [Web NFC](#web-nfc)
  - [Reading NFC tags](#reading-nfc-tags)
  - [Writing NFC tags](#writing-nfc-tags)
- [Content Indexing](#content-indexing)
- [Shape Detection](#shape-detection)
  - [Detecting barcodes](#detecting-barcodes)
  - [Detecting faces](#detecting-faces)
  - [Detecting text](#detecting-text)
- [Transport](#transport)
- [Conclusion](#conclusion)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction

The web platform as advanced to an application platform.
With the appearance of HTML5, web applications 
cross-platform

However, there is still a gap between the capabilities of native applications and web apps, the so called web app gap.
With the Web Capabilities project also known as Project Fugu, the Chromium contributors try to close the web app gap by evaluating, specifying, and implementing APIs in browsers with native power.

[Progressive Web Apps](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) have
Technical foundations such as Service Workers, the Web App Manifest, and IndexedDB. 
Applications can run offline.
However, web applications can only call native functionality

Special emphasis on security and privacy
Most APIs require the website to be sent over a secure connection (HTTPS).
Some of them require a user gesture, such as a click or key press, to prevent fraud (ads).

In this chapter, we'll have a look at different modern web APIs, and the state of web capabilities in 2020. 

## Web Share API

The [Web Share API](https://web.dev/web-share/) allows you to share content with other applications installed on the system using the native share dialog.

## Sharing Text and URLs

```
(🐡 Launched) Web Share (No Files)
```

With Level 1 of Web Share API, web apps can share a title, text, and a URL with other applications.
Chromium forks on Android and Safari currently support this API.
Using Web Share API is as easy as calling the `share()` method on the `navigator` object.
This method takes an object with a title, text, or a URL to share; at least one option must be given. (TODO: True?)
Next, the native share window will open.
You can await the result of the operation.

```
await navigator.share({ title: 'Test', text: 'Web Almanac 2020' });
```

(TODO: Usage)

## Sharing Files

```
(🐡 Launched) Web Share (Containing Files)
```

Level 2 of Web Share API additionally allows you to share files as well.
Chromium forks currently support this API on Android, Safari is adding support with the release of iOS 14. (TODO: True?)
Level 2 adds a new method called `canShare()` on the `navigator` object.
With the help of this method, you can check if the user agent supports sharing certain content.
Also, you can feature detect support for Level 2 by checking for the existence of the `canShare()` method.
If the content can be shared, you simply add it to the `files` array of the options object, like so: (TODO: more code?)

```
await navigator.share({ files: [myFile1] });
```

(TODO: Usage)

To receive data shared from other applications, web apps can implement the [Web Share Target API](https://web.dev/web-share-target/).
This API allows installed web applications to register with the operating system as a share target.
The web application appears in the native share dialog, and can receive content shared from other apps.

## Clipboard Access

With the help of the `document.execCommand()` method, websites could already access with the user's clipboard.
However, this approach is somewhat restricted, as the API is synchronous, and it can only interact with selected text in the DOM.
This is where the [Async Clipboard API](https://web.dev/async-clipboard/) comes in.
The new API is not only asynchronous, meaning it doesn't block the page for large chunks of data, but also allows for images to be copied to or pasted from the clipboard in supported browsers.

### Read Access

```
(🐡 Launched) Async Clipboard Read
```

The Async Clipboard API provides two methods for reading content from the clipboard:
A shorthand method for plain text, called `navigator.clipboard.readText()`, and a method for arbitrary data, called `navigator.clipboard.read()`.
Currently, most browsers only support PNG images (TODO: True?).
Due to privacy reasons, reading from the clipboard always requires the user's consent.

(TODO: Analyze data)

### Write Access

```
(🐡 Launched) Async Clipboard Write
```

On the other hand, the Async Clipboard API offers two methods for writing content to the clipboard.
Again, there's a shorthand method for plain text, called `navigator.clipboard.writeText()`, and one for arbitrary data called `navigator.clipboard.write()`.
In Chrome, writing to the clipboard while the browsing context is active (i.e., tab is open or window) does not require permission.
Trying to write to the clipboard when the website is in the background does, however.

(TODO: Analyze data)

With the Raw Clipboard Access API, the Async Clipboard API should be further enhanced by allowing arbitrary data to be copied from or pasted to the clipboard. (TODO: More Info)

## File System Access

```
(🧪 Origin trial) Native File System
```

One of the key features for productivity applications such as IDEs, image editors, or office apps is file system access.
With `input[type=file]` and `a[download]`, the web already has mechanisms to read files from the file system and save them back to the Downloads folder.
The aforementioned applications typically allow you to open single files or a folder, make some changes, and overwrite them in place.
The Native File System API enables you to read and write files from the local file system, open directories and enumerate their contents in a secure and privacy-conserving manner.

To do so, the API introduces a new asynchronous method on the global `window` object called `chooseFileSystemEntries()`.
This method takes an `options` object that can be used to specify the operation (save/read), kind (file/directory), or file extensions.
After calling this method, the browser will show a dialog box where the user has to select the target file or directory from.
For security reasons, not all directories can be selected, such as system folders or directories containing sensitive data.

(TODO: Analyze data)

The availability of Native File System API could bring a lot of applications and experiences to the web that currently need to be shipped in a native wrapper such as Electron. (TODO: Slack, VS Code, Skype as examples?)

## Contact Picker

```
(🐡 Launched) Contact Picker
```

The [Contact Picker API](https://web.dev/contact-picker/) allows you to access single contacts on the user's system.
It does not allow you to request the entire address book or to check numbers against your contacts though.
Comparable to the Native File System API, a dialog box will appear first, where the user has to pick the contact they want to share with the website.

The Contact Picker API introduces a new `ContactManager` interface on the `navigator` object.
Calling `navigator.contacts.select()` returns a promise and opens the contact picker.
This method takes two arguments:
First, an array of contact information the website wants to access, such as `name`, `email`, `tel`, `address`, or `icon`.
As a second argument, the method takes an `options` object which can be used to request multiple contacts by setting the `multiple` property to `true`.
When the user selects contacts and decides to share them with the website, the promise will resolve and return the contact information back to the website.

(TODO: Analyze)

## Storage

Web browsers allow you to store data on the user's system in different ways, such as Cookies, in the Indexed Database (IndexedDB), the Service Worker's Cache Storage or Web Storage (Local Storage, Session Storage).
In modern browsers, you can easily store hundreds of megabytes and even more depending on the browser.
When browsers run out of space, they can clear data until the system is no longer over the limit, which can lead to data loss.

Thanks to the [StorageManager API](https://web.dev/storage-for-the-web/#check-available), browsers no longer behave like a black box in that regard:
This API allows you to estimate the remaining space available, and to opt-in to persistent storage, meaning that the browser will not clear your website's data when disk space is low.
Therefore, the API introduces a new `StorageManager` interface on the `navigator` object.

### Estimate the available storage

```
(🐡 Launched) Storage Estimation
```

You can have the browser estimate the available storage by calling `navigator.storage.estimate()`.
This method returns a promise resolving to an object with two properties:
`usage` shows the number of bytes used by the application and `quota` contains the maximum number of bytes available.

(TODO: Analyze)

### Opt-in to persistent storage

```
(🐡 Launched) Persistent Storage
```

There are two categories of web storage: "Best Effort" and "Persistent", with the first being the default.
When a device is low on storage, the browser automatically tries to free up best effort storage.
For instance, Firefox and Chromium-based browsers evict storage from the least recently used origins.
This however poses a risk of losing critical data.
To prevent eviction, you can opt for persistent storage.
In this case, the browser will not clear the storage, even when space is low.
Users can still choose to clear up the storage manually.
To opt for persistent storage, you call the `navigator.storage.persist()` method.
Depending on the browser and site engagement, a permission prompt may show, or the request will be automatically accepted or denied.

(TODO: Analyze)

## Notifications

With the help of the Push and Notifications API, web applications have long been able to receive push messages and display notification banners.
However, some parts are missing:
Up until now, push messages had to be sent via the server; they cannot be scheduled offline.
Also, web applications installed to the system cannot show badges on their icon.
The Badging and Notification Triggers API want to enable both scenarios.

### Badging

```
(🐡 Launched) Badge Set
(🐡 Launched) Badge Clear
```

On several platforms, it's common for applications to present a badge on the application's icon indicating the amount of open actions:
For instance, the badge could show the number of unread emails, notifications or to-do items to complete.
The [Badging API](https://web.dev/badging-api/) allows your installed web application to show such a badge on its icon.
By calling `navigator.setAppBadge()`, you can set the badge.
This method takes a number to be shown on the application's badge.
The browser then takes care of displaying the closest possible representation on the user's device.
If you don't specify a number, a generic badge will be shown (e.g., a white dot on macOS).
Calling `navigator.clearAppBadge()` removes the badge again.
Badging API is a great choice for email clients, social media apps, or messengers.

(TODO: analyze data)
(TODO: Twitter?)

### Scheduling Notifications

```
(🧪 Origin trial) Notification Triggers
```

The Push API requires the user to be online to receive a notification.
Some applications, such as games, calendar or alarm applications, could also determine the target date for a notification locally and schedule it.
To allow this feature, the Chrome team experimented with a new API called [Notification Triggers](https://web.dev/notification-triggers/).
This API adds a new property called `showTrigger` to the `options` map that can be passed to the `showNotification()` method on the service worker's registration.
The API would allow for different kinds of triggers.
For scheduling a notification based on a certain date and time, you can create a new instance of a `TimestampTrigger` and pass the target timestamp to it:

```js
registration.showNotification('Title', {
  body: 'Message',
  showTrigger: new TimestampTrigger(timestamp)
});
```

(TODO: analyze data)

Due to lack of developer interest, the Chrome team decided to pause the further development of Notification Triggers in July 2020.

## Wake Lock API

```
(🐡 Launched) Screen Wake Lock
```

To save energy, mobile devices darken the screen backlight and eventually turn of the device's display, which makes sense in most cases.
There are scenarios however, where the user may want the application to not turn off the display, for instance, when reading a recipe while cooking with dirty hands.

In a [Wake Lock API case study on BettyCrocker.com](https://web.dev/betty-crocker/), the purchase intent indicators increased by about 300 percent. (TODO: More)

## Idle Detection API

```
(🧪 Origin trial) Idle Detection
```

## Web OTP

```
(🐡 Launched) Web OTP
```

For Two-Factor Authentication (TFA or 2FA), services often send a text message to the user's device. 
To improve the experience, the operating system can share the one-time password with the browser, so the user doesn't have to switch apps and copy-paste the OTP.

## Text Fragment

Text fragments help readers to identify the relevant parts 

```
(🐡 Launched) Text Fragment
```

## Periodic Background Sync

When an application was closed, it cannot communicate

In some scenarios, developers still want to synchronize data on a more or less regular basis, for instance, by …

```
(🐡 Launched) Periodic Background Sync Register
(🐡 Launched) Periodic Background Sync
```

## Integration with native app stores

```
(🐡 Launched) Get Installed Related Apps
```

## Devices

TODO: These APIs require a user gesture and probably cannot be evaluated.

```
(🐡 Launched) Web USB
(🐡 Launched) Web Bluetooth
(🐡 Launched) Web MIDI
(🧪 Origin trial) Web Serial
```

## Web NFC

### Reading NFC tags

```
(🧪 Origin trial) Web NFC (Read)
```

### Writing NFC tags

```
(🧪 Origin trial) Web NFC (Write)
```

## Content Indexing

```
(🧪 Origin trial) Content Indexing
```

## Shape Detection

The [Shape Detection API](https://web.dev/shape-detection/) allows you to detect barcodes, faces, or text in images.

### Detecting barcodes

```
(🐡 Launched) Shape Detection (Barcode)
```

`BarcodeDetector`

### Detecting faces

```
(🚩 Behind flag) Shape Detection (Face)
```

`FaceDetector`

### Detecting text

```
(🚩 Behind flag) Shape Detection (Text)
```

`TextDetector`

## Transport
```
(🧪 Origin trial) WebSocketStream
(🧪 Origin trial) QuicTransport
```

## Conclusion

The state of web capabilities 2020 is… (TODO)

- security
- privacy
- developer interaction
