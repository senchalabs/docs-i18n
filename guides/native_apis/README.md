# Accessing Native APIs with Ext.device

One of the most important benefits of native packaging is the access to native APIs. Sencha Touch provides an access to native APIs through its `Ext.device` API. The `Ext.device` API does not itself provide the native API capabilities, it is just a wrapper that can be used to access native APIs powered by the Sencha Packager or third party solutions, such as PhoneGap and the iOS Simulator. At the time of writing, `Ext.device` provides the following four APIs:

- {@link Ext.device.Connection} - indicates whether the device has a current connection to the internet.
- {@link Ext.device.Notification} - enables you to use native notification windows (the native version of {@link Ext.Msg}).
- {@link Ext.device.Orientation} - provides information on the device's current orientation.
- {@link Ext.device.Camera} - allows your app to take pictures or select images from the camera library (with the user's permission).

## Getting Started

Because we want to keep our apps small, and because native APIs only apply in native packaged apps, the `Ext.device` API is not included in the standard build of Sencha Touch. When you want to use any of the Device APIs , you must require them using `Ext.require`:

    Ext.require('Ext.device.Connection');

    Ext.application({
        name: 'MyApp'
        // the rest of your app follows
    });

## Connection

The Connection API is used to determine whether the device is currently online. It exposes two main methods - `isOnline` and `getType`:

    if (Ext.device.Connection.isOnline()) {
        Ext.Msg.alert('You are currently connected via ' + Ext.device.Connection.getType());
    } else {
        Ext.Msg.alert('You are not currently connected');
    }

This is probably the simplest of the APIs offered by `Ext.device`. If you load this code on a device or inside the simulator, you will see a Sencha Touch message box appearing, indicating that you are offline, or indicating your connection type (wifi, 2g, 3g, and so on), if you are online. See the {@link Ext.device.Connection} docs for a full list of possible connection types.

## Notification

Notifications are used to show native dialog boxes. Notifications work very much like {@link Ext.Msg} and can be configured in many different ways. For example, the following example shows how to display a native notification box with a title and a message:

    Ext.device.Notification.show({
        title: 'One Button',
        message: 'This is a simple notification with one button.'
    });

This code causes a native notification window to appear as shown in the following image:

{@img notifications-simple.png}

The following code displays a native message box with the configured title and message. It also adds an OK button, so the user can dismiss the message. When the user taps on the button, the callback is called - in this example we respond to the button tap by opening a second notification:

    Ext.device.Notification.show({
        title: 'One Button',
        message: 'This is a simple notification with one button.',

        callback: function(button) {
            //When the user taps a button, show another notification
            Ext.device.Notification.show({
                message: 'You pressed: "' + button + '"'
            });
        }
    });

We did not provide a title for the second message, which is fine - it just shows the message and an OK button. We can customize the buttons that are presented to the user as follows:

    Ext.device.Notification.show({
        title: 'Multiple Buttons',
        message: 'This is a notification with multiple buttons.',
        buttons: ["Cancel", "Login", "Another"],

        callback: function(button) {
            //When the user taps a button, show another notification
            Ext.device.Notification.show({
                message: 'You pressed: "' + button + '"'
            });
        }
    });

This time we provided three button text strings in the *buttons* array - when we run this code we now see the buttons stacked on top of one another as shown by the following image:

{@img notifications-custom.png}

### Vibration

Another capability of the notification API is to vibrate the device. You can make this happen as shown in the following code:

    Ext.device.notification.vibrate();

This method call causes the device to vibrate for a short time, and can be called as often as desired.

## Camera

The Camera API allows you to take pictures using the phone's camera, or alternatively select an image from the existing photo library available on the phone. The following code sample shows how to take a new picture:

    Ext.device.Camera.capture({
        source: 'camera',
        destination: 'file',

        success: function(url) {
            //show the newly captured image in a full screen Ext.Img component:
            Ext.create('Ext.Img', {
                src: url,
                fullscreen: true
            });
        }
    });

Note that we specified two options (apart from our *success* callback) - source and destination. We can set *source* to either 'camera' or 'library' - the first option opens the camera to take a new picture, the second opens a native photo browser window to select an existing image. This example opens the camera app then saves the new image to a file, finally calling our *success* function with the local url to that file.

The other options that can be passed into the {@link Ext.device.Camera#capture} call are the following:

- `success` - the function to call if an image was successfully acquired.
- `failure` - the function to call if an image could not be acquired.
- `scope` - the scope to run the two previously mentioned functions in (sets the value of `this`).
- `quality` - a hint provided to the camera about the compression quality of the image (0-100).
- `width` - the desired width of the new image, in px.
- `height` - the desired height of the new image, in px.
- `source` - can be 'camera' or 'library' - 'camera' takes a new picture, 'library' chooses an existing one.
- `destination` - can be 'file' or 'data' - file returns a local url to the new image, 'data' returns a Base64 encoded string of the image.
- `encoding` - can be 'jpeg', 'jpg' or 'png' - the first two options encode the image as a JPEG, the third as a PNG.

Any number of options can be passed into your {@link Ext.device.Camera#capture} call.

## Orientation

The orientation API yields information about the current orientation of the device. In the following example we register a handler function that is called every time a device orientation change is detected:

    Ext.device.Orientation.on('orientation', function(e) {
        var alpha = Math.round(e.alpha),
            beta = Math.round(e.beta),
            gamma = Math.round(e.gamma);

        console.log(alpha, beta, gamma);
    });

This example provides a stream of console messages with the alpha, beta, and gamma orientation of the device. These values correspond to the three dimensions the device can be pivoted around, and are expressed in degrees ranging from 0 to 360.

## Custom Scheme URLs

You can use custom scheme URLs to launch an application from other applications:

    Ext.application({
        name: 'Sencha',
        requires: ['Ext.device.Device'],
        launch: function() {
            if (Ext.device.Device.scheme) {
                // the application was opened via another application. Do something:
                alert('Applicaton pened via another application: ' + Ext.device.Device.scheme.url);
            }

            // Listen for future changes
            Ext.device.Device.on('schemeupdate', function(device, scheme) {
                // the application was launched, closed, and then launched another from another application
                // this means onReady will not be called again (because the application is already running in the 
                // background) - but this event will be fired
                alert('Applicated reopened via another application: ' + scheme.url);
            }, this);
        }
    });

In order for this code to work, when packaging your application you must add add the custom URLs you would like to use. You can do this by adding the following code into the `rawConfig` property inside your `package.json` file (Sencha Native Packager configuration file):

    {
        ...
        "rawConfig": "<key>CFBundleURLTypes</key><array><dict><key>CFBundleURLSchemes</key><array><string>sencha</string></array><key>CFBundleURLName</key><string>com.sencha.example</string></dict></array>"
        ...
    }

You can change the available URL schemes and the application identifier from the previous example.

You can then test the code by packaging and installing the application onto a device/iOS Simulator, opening Safari and typing: `sencha:testing`. The application will launch and it will `alert` the specified URL.

__Note:__ This currently only works with the Sencha Native Packager. If you attempt to listen to this event when packaged with PhoneGap or simply in the browser, it will not function.

## Packaging

Now that you have an understanding of how to use the `Ext.device` API to access native device functionality, proceed to the guides on packaging your app for [iOS](#!/guide/native_packaging) and [Android](#!/guide/native_android) to start testing the app on a real device.
