# पॉवर मॉनिटर

> Monitor power state changes.

प्रक्रिया: [Main](../glossary.md#main-process)


This module cannot be used until the `ready` event of the `app` module is emitted.

उदाहरण के लिए:

```javascript
const { app, powerMonitor } = require('electron')

app.whenReady().then(() => {
  powerMonitor.on('suspend', () => {
    console.log('The system is going to sleep')
  })
})
```

## इवेंट्स

The `powerMonitor` module emits the following events:

### Event: 'suspend' _macOS_ _Windows_

Emitted when the system is suspending.

### Event: 'resume' _macOS_ _Windows_

Emitted when system is resuming.

### Event: 'on-ac' _macOS_ _Windows_

Emitted when the system changes to AC power.

### Event: 'on-battery' _macOS_  _Windows_

Emitted when system changes to battery power.

### Event: 'shutdown' _Linux_ _macOS_

Emitted when the system is about to reboot or shut down. If the event handler invokes `e.preventDefault()`, Electron will attempt to delay system shutdown in order for the app to exit cleanly. If `e.preventDefault()` is called, the app should exit as soon as possible by calling something like `app.quit()`.

### Event: 'lock-screen' _macOS_ _Windows_

Emitted when the system is about to lock the screen.

### Event: 'unlock-screen' _macOS_ _Windows_

Emitted as soon as the systems screen is unlocked.

## मेथड्स

The `powerMonitor` module has the following methods:

### `powerMonitor.getSystemIdleState(idleThreshold)`

* `idleThreshold` Integer

Returns `String` - The system's current state. Can be `active`, `idle`, `locked` or `unknown`.

Calculate the system idle state. `idleThreshold` is the amount of time (in seconds) before considered idle.  `locked` is available on supported systems only.

### `powerMonitor.getSystemIdleTime()`

Returns `Integer` - Idle time in seconds

Calculate system idle time in seconds.