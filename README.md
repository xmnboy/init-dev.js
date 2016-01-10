# `init-dev.js`

Detect various _ready_ events within a variety of webviews.
Intended for use with Cordova (aka PhoneGap) apps.

The functions in this file are designed to reliably detect various _ready_ events
within a variety of containers (Intel XDK "legacy" container, Cordova 3+ container,
standard browser, App Preview, Crosswalk, etc.). It _unifies_ the commonly used
ready events and is very helpful for moving a _web app_ to a _hybrid app_ scenario.

This file has no external dependencies. It will generate a custom `app.Ready` event
that you can use to start your application, rather than waiting on a `deviceready`
or `documentready` or window `load` or similar events.

You should not have to modify anything in this file to use it. See the example
`index.html` file that accompanies this file for recommendations on the best placement
of this file relative to other files and for recommendations regarding the loading
of other JavaScript files.

There are many `console.log()` messages contained within this file. They can be used
to debug initialization problems and understand how the code in this file works.
It is highly recommended that you leave them in this file, they will not unduly
slow down or burden your application. The `console.log()` messages in this file
will only be executed if you set the `dev.Log` variable to `true` (see source).

There are many comments in this file and the accompanying `index.html` sample file.
Please read the comments within for details and for further documentation.

There is an object named `dev.isDeviceready` which is very useful to inspect from
a debug console to determine the timing of various ready events. It is filled with
relative _timings_ (in milliseconds) that can help you understand how long it is
taking your app to get started (at least, the `cordova.js` part of your app).
```JavaScript
dev.isDeviceReady = {                   // listed in approximate expected order
    a_startTime______:dev.timeStamp(),  // when we started execution of this module
    b_fnDocumentReady:false,            // detected document.readyState == "complete"
    c_cordova_ready__:false,            // detected cordova device ready event
    d_xdk_ready______:false,            // detected Intel XDK device ready event
    e_fnDeviceReady__:false,            // entered onDeviceReady()
    f_browser_ready__:false             // detected browser container
} ;
```
The two most interesting elements in that object are `b_fnDocumentReady` and
`c_cordova_ready`. The object names have extra underscore characters to make it
easier to compare the results when you inspect it on the JavaScript debug console.
The `d_xdk_ready` timing should normally be `false` since the Intel XDK "legacy"
containers have been retired, but can show up if the old `intel.xdk.base` plugin
is included as part of your app.

> "dev" means "device" in this context, not "develop," because it grew out
> of a desire to build a more reliable and flexible `deviceready` detector.

# `index.html`

This file is provided simply as an example of where the `init-dev.js` file should
be located within your `index.html` file. You do not need to use this `index.html`
file to use the `init-dev.js` file -- it is provided for illustration only.

Recommended JavaScript library load order for hybrid Cordova apps in your `index.html` file:

  * _Device-Independent_ JavaScript libraries (see notes below).
  * _Optional_ weinre debug script tag for remote console debug.
  * Cordova/PhoneGap device JavaScript library.
  * _Device-Dependent_ JavaScript libraries (see notes below).
  * Application JavaScript \<script\> tags and libraries.
  * _Optional_ weinre debug script tag for remote console debug.

In complex projects, the JavaScript load order is important. You must insure that the underlying
device API native code finishes its init, which usually takes longer than the webview init...

**VERY IMPORTANT:** notice that all of the libraries used in this project are located within the app's
local directories, which means they will get bundled with the app. They are NOT being pulled in
over the net. In most cases, this is what you should be doing when you build a hybrid mobile app.
This insures that you always use the JS code that you debugged against AND that you are not requiring
a data connection (network connection) to get the app started. If your app startup requires a data
connection to initialize and start interaction with the user, lack of a reliable network connection
could be disasterous. Not to mention it generally results in a slower load time. Loading locally is
is much friendlier to your end user's data plan and battery.  :smile:

**NOTE:** do not use a directory prefix with the `cordova.js` file - it is inserted automatically
by the build system, emulator and other tools, and is assumed to be in the `index.html` source directory.
You will not find this JS file anywhere in your project, it is a _phantom_ library. If you do see
copies of this file as part of your project it should be removed.

**LIBRARY NOTE:** If you are using a large number of JavaScript libraries, especially third-party libraries
(like jQuery, {{ mustache }}, Underscore, etc.) that are _independent_ of the device APIs provided
by the Cordova library and plugins - and they are independent of your application - your
app initialization will be most successful if you load these libraries BEFORE the Cordova JS file,
in the <head> section of your `index.html` file. Obviously, any code that depends on Cordova APIs
must be loaded AFTER the `cordova.js` library.

Libraries that are _independent_ of the device APIs are libraries that you could use in a desktop
browser in a normal web page or web app. _Dependent_ libraries are, most likely, your own code
that you've written specifically to work with the Cordova device APIs. In some cases,
if your device-dependent code requires access to device-independent code to get started, you may
have to use something like CommonJS to force the device-dependent code to wait for the device-
independent code to initialize, otherwise you may have trouble getting your app started.

Because of this added dependency on the underlying native code (device) initialization, you should
not use the `documentready` event to start your application. You should wait for the `deviceready`
event before you begin your application (if it uses any device APIs); in practice, it is best to wait
for both. (See the `init-dev.js` file for an example of how to do this, it generates a custom
`app.Ready` event that you can trigger off of and just forget about the other events.)

**NOTE:** _any_ library that redefines `addEventListener()` or fiddles with outstanding events may
interfere with capturing the `deviceready` event and should, therefore, be placed **BEFORE**
the `cordova.js` library in the load order.

> If you use `weinre` for debugging, you may have to experiment with the placement of your `weinre`
> script. Some recommended locations are shown within. If these locations do not work, you may have
> to experiment. The optimum placement can be app-specific, primarily as a function of the included
> JavaScript libraries and your initialization code.
