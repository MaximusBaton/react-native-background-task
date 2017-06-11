# react-native-background-task

**NB**: this is a fork of [react-native-background-task](https://www.npmjs.com/package/react-native-background-task)

Register a single JS background task to run at periodic intervals, on Android and iOS React Native apps, even when the app is closed.

This builds on top of the native modules provided by the following two libraries:

- **iOS**: [react-native-background-fetch](https://github.com/transistorsoft/react-native-background-fetch), which uses the iOS-specific `Background Fetch` technique.
- **Android**: [react-native-background-job](https://github.com/vikeri/react-native-background-job), which provides a job scheduler on RN's built-in [Headless JS](https://facebook.github.io/react-native/docs/headless-js-android.html) (Android only).

To achieve a unified API, this package exposes the lowest common denominator (e.g. only support for a single task, even though Android can support multiple).

For more per-platform flexibility, those libraries should be used individually.

## Installation

```bash
$ npm install --save react-native-background-task
```

* **iOS**: Follow native iOS module installation instructions from
  [react-native-background-fetch](https://github.com/transistorsoft/react-native-background-fetch)
* **Android**: Follow native Android module installation instructions from
  [react-native-background-job](https://github.com/vikeri/react-native-background-job)

## API

### `register(task, options)`

Define the task that this module should be executing.

- Will overwrite any previously registered task.
- Should be called at the root of your main app entrypoint file.
- Should `console.error` if it can't register the task

Parameters:

* **`task`**: **required** `() => void` - Function to be executed in the background
* **`options`**: `?object` - Any configuration you want to be set with
  the task.  Note that most of these will only work on one platform.
  
  * **`period`** `number` - (Android only) Desired number of seconds between each
    execution of the task.  Even on Android, the OS will only take this as a
    recommendation, and will likely enforce a minimum of 15 minutes (similar to
    iOS).  Default is 9000 (15 minutes)
  * **`timeout`** `number` - (Android only) Number of seconds the task will have
    to execute.  iOS has a hardcoded limit of 30 seconds.  Default 30 seconds.

### `cancel()`

Cancels any currently registered task.

### `finish()`

Must be called at the end of your task to indicate to the OS that it's
finished.  (Only required on iOS, no-op on Android).

## Example

### Simple

```js
import React from 'react'
import { Text } from 'react-native'
import BackgroundTask from 'react-native-background-task'

BackgroundTask.register(() => {
  console.log('Hello from a background task')
  BackgroundTask.finish()
})

class MyApp extends React.Component {
  render() {
    return <Text>Hello world</Text>
  }
}
```