# react-native-spotlight-search

A React Native module for iOS that provides Spotlight search functionality. This allows you to index content from within your React Native app so that it appears in the iOS device's Spotlight search index, potentially increasing the exposure of your app.

## Current Features

* Adding items.
* Updating items.
* Deleting items.
* Register a callback to handle when a search item is tapped.
* Basic support for images (offline, local images).

![Spotlight Search Demo](http://i.imgur.com/tbI3yAs.gif)

## Installation

With yarn (recommended):

`$ yarn add react-native-spotlight-search`

Or with NPM:

`$ npm install react-native-spotlight-search --save`

### iOS

#### With `react-native link`

`$ react-native link`

#### Manually

Simply add `RCTSpotlightSearch.xcodeproj` to **Libraries** and add `libRCTSpotlightSearch.a` to **Link Binary With Libraries** under **Build Phases**. [More info and screenshots about how to do this is available in the React Native documentation](http://facebook.github.io/react-native/docs/linking-libraries-ios.html#content).

### In Your AppDelegate (Optional)

If you wish to be able to handle search item tapped callbacks, you'll need to add the following code to your AppDelegate file:

```
#import "RCTSpotlightSearch.h"

- (BOOL)application:(UIApplication *)application continueUserActivity:(nonnull NSUserActivity *)userActivity restorationHandler:(nonnull void (^)(NSArray * _Nullable))restorationHandler {
  [RCTSpotlightSearch handleContinueUserActivity:userActivity];
  return YES;
}
```

If Xcode complains about being unable to find the header file, please ensure that your project's header search includes the following:

`$(SRCROOT)/../node_modules/react-native-spotlight-search`

Like this:

![Header Search Paths](http://i.imgur.com/r69EMcQ.png)

## Usage

First up, import the module:

```import SpotlightSearch from 'react-native-spotlight-search';```

### Indexing Items

You can either add an array of items:

```
SpotlightSearch.indexItems([
    {
        title: 'Strawberry',
        contentDescription: 'A sweet and juicy fruit.',
        uniqueIdentifier: '1',
        domain: 'fruit',
        thumbnailName: 'strawberry',
    },
    {
        title: 'Kiwi',
        contentDescription: 'Not a type of bird.',
        uniqueIdentifier: '2',
        domain: 'fruit',
        thumbnailName: 'kiwi',
    },
]);
```

Or individual items:

```
SpotlightSearch.indexItem({
    title: 'Strawberry',
    contentDescription: 'A sweet and juicy fruit.',
    uniqueIdentifier: '1',
    thumbnailName: 'strawberry',
});
```

#### Search Item Properties

| Property | Description | Type | Required |
|---|----|---|---|
|**`title`**|The title of the search item.|`string`|Yes|
|**`contentDescription`**|A description which appears below the title in the search results.|`string`|No|
|**`uniqueIdentifier`**|A unique and stable identifier. Used to refer to the item. |`string`|Yes|
|**`domain`**|A string for grouping related items together in a way that makes sense. Not displayed to the user. |`string`|Yes|
|**`thumbnailName`**|A local file name/key to a thumbnail image. See [A Note About Thumbnails](#a-note-about-thumbnails).|`string`|No|
|**`keywords`**|An array of keywords which can be used to help inform the search index. Not visible to the user.|`[string]`|No|

### Updating Items

Simply use the same method as adding items. Be sure to reference the same key when indexing the item so that any new metadata changes will be reflected in the Spotlight index.

### Removing Items

Items can be removed by identifier:

```
SpotlightSearch.deleteItemsWithIdentifiers(["1", "2"]);
```

Or by domain:

```
SpotlightSearch.deleteItemsInDomains(["fruit"]);
```

Alternatively, you can delete _all_ items indexed by your app:

```
SpotlightSearch.deleteAllItems();
```

### Promises

All API index and delete methods are asynchronous and return promises. You can chain things like this:

```
SpotlightSearch.deleteAllItems().then(() => {
  SpotlightSearch.indexItem({
      title: 'Strawberry',
      contentDescription: 'A sweet and juicy fruit.',
      uniqueIdentifier: '1',
      thumbnailName: 'strawberry',
  });
});
```

### Handling User Interactions

You can choose to add a custom handler that will be invoked in the event of a user tapping one of the search items in the Spotlight results:

```
SpotlightSearch.searchItemTapped((uniqueIdentifier) => {
  alert(`You tapped on ${uniqueIdentifier}!`);
});
```

Optionally, if you want to capture the search item that was tapped to open the app (perhaps the listener was set after the event was triggered):

```
SpotlightSearch.getInitialSearchItem().then((uniqueIdentifier) => {
  alert(`You tapped on ${uniqueIdentifier} and opened the app!`);
});

```

The parameter will be the ```uniqueIdentifier``` that the item was indexed with. You can use this to lookup the item and display information about it, e.g. by navigating to a relevant page in your app.

## A Note About Thumbnails

Currently, in order to use an image it must exist locally on the device. This is a limitation of iOS rather than of this library.

This means it is not possible to use images hosted on the web. Additionally, there is a limitation that you cannot use a statically imported image managed by React Native. Instead, you _must_ manually add the image to your app's asset catalog.

Suggestions on how to improve this are welcome!

## To-do

* Support additional built in types (location etc).
* Improve image handling.
* Public links.
* Initial release.
* New iOS 10 features.

PRs welcome ;-)
