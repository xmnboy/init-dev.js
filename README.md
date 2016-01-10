# init-dev.js
Detect various "ready" events within a variety of webviews. Intended for use within Cordova (aka PhoneGap) apps.

The functions in this file are designed to reliably detect various "ready" events
within a variety of containers (Intel XDK "legacy" container, Cordova 3+ container,
standard browser, App Preview, Crosswalk, etc.). It "unifies" the commonly used
ready events and is very helpful for moving a "web app" to a "hybrid app" scenario.

This file has no external dependencies. It will generate a custom "app.Ready" event
that you can use to start your application, rather than waiting on a "device ready" 
or "document ready" or "window load" or similar events.

You should not have to modify anything in this file to use it. See the example
index.html file that accompanies this file for recommendations on the best placement 
of this file relative to other files and for recommendations regarding the loading 
of other JavaScript files.

There are many console.log messages contained within this file. They can be used
to debug initialization problems and understand how the code in this file works.
It is highly recommended that you leave them in this file, they will not unduly
slow down or burden your application. The console.log messages in this file
will only be executed if you set the dev.Log variable to "true" (see source).

There are many comments in this file and the accompanying index.html sample file.
Please read the comments within for details and for further documentation.

BTW: "dev" means "device" in this context, not "develop," because it grew out
of a desire to build a more reliable and flexible "device ready" detector.
