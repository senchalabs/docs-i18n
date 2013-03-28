# Using Device Profiles

Today's mobile web applications are expected to work on a wide variety of devices, spanning from the smallest mobile phones to the largest tablets. These devices have a wide range of screen resolutions and are used in different ways. People tend to use phone apps while out of the house to rapidly gather some information or to perform some action - often in a very short time. Tablet apps are more likely to be used for longer periods of time, usually within the home or somewhere else where they can sit for a long time.

All of this means that people expect different app experiences on different devices. However, much of your application logic and assets can be shared between the different experiences. Writing separate apps for each platform is time consuming, error-prone and plain boring. Thankfully, Device Profiles give us a simple way to share  between device types as much code as needed, while making it easy to customize behavior, appearance, and workflows for each device.

## Setting Up Profiles

Device Profiles exist within the context of an {@link Ext.app.Application Application}, for example if we wanted to create an email app with phone and tablet profiles, we could define our app.js shown in the following code sample (see the [Intro to Apps guide](#!/guide/apps_intro) if this is not familiar):

    Ext.application({
        name: 'Mail',

        profiles: ['Phone', 'Tablet']
    });

Since we did not give our Application a {@link Ext.app.Application#launch launch function}, at the moment it is only going to load those two Profiles. By convention, it expects to find them in *app/profile/Phone.js* and *app/profile/Tablet.js*. The following code shows the Phone profile:

    Ext.define('Mail.profile.Phone', {
        extend: 'Ext.app.Profile',

        config: {
            name: 'Phone',
            views: ['Main']
        },

        isActive: function() {
            return Ext.os.is.Phone;
        }
    });

The Tablet profile follows the same pattern. In the Phone profile we only supplied three pieces of information - the Profile {@link Ext.app.Profile#name name}, the optional set of additional {@link Ext.app.Profile#views views} to load when this Profile is activated, and an {@link Ext.app.Profile#isActive isActive} function.

The {@link Ext.app.Profile#isActive isActive} function determines if a given profile should be active on the device your app runs on. By far the most common approach is to create profiles for Phone and Tablet, using the built-in `Ext.os.is.Phone` and `Ext.os.is.Tablet` properties. You can write any code you like in the `isActive` function, as long as it always returns `true` or `false` for the device it is running on.

## Determining the Active Profile

Once the Profiles have been loaded, their isActive functions are called in turn. The first one that return true is the Profile that the Application boots with. This Profile is then set to the Application's {@link Ext.app.Application#currentProfile currentProfile}, and the Application prepares to load all of its dependencies - the models, views, controllers, and other classes that constitute the app. It does this by combining its own dependencies with those specified in the active profile.

For example, let us amend our previous Application so that it loads its own Models and Views:

    Ext.application({
        name: 'Mail',

        profiles: ['Phone', 'Tablet'],

        models: ['User'],
        views: ['Navigation', 'Login']
    });

Finally, when we load the app on a phone, the Phone profile is activated and the application loads the following files:

* app/model/User.js
* app/view/Navigation.js
* app/view/Login.js
* app/view/**phone**/Main.js

The first three items are specified in the Application itself - the User model plus the Navigation and Login views. The fourth item is specified by the Phone Profile and follows a special form. By convention, classes that are specific to a Profile are expected to be defined in a subdirectory with the same name as the Profile. For example, the 'Main' view specified in the Phone Profile is loaded from *app/view/**phone**/Main.js*, whereas if we had defined 'Main' in the Application, it would be loaded from *app/view/Main.js*.

The same applies to all models, views, controllers, and stores loaded in a Profile. This is important as it enables you to easily share behavior, view logic, and more between profiles (see the specializing views and controllers sections below). If you need to load classes that do not comply with this convention, you can specify the full class name instead:

    Ext.define('Mail.profile.Phone', {
        extend: 'Ext.app.Profile',

        config: {
            name: 'Phone',
            views: ['Main', 'Mail.view.SpecialView'],
            models: ['Mail.model.Message']
        },

        isActive: function() {
            return Ext.os.is.Phone;
        }
    });

As evident from this example, you can mix and match fully-qualified class names (e.g. *'Mail.view.SomeView'*) and relatively specified class names (e.g. *'Main'*, which becomes *'Mail.view.phone.Main'*). Be aware that all models, views, controllers, and stores specified for a Profile are treated this way. This means if there are Models or Stores that you want to load for Tablets only, but do not want to create classes like *Mail.model.tablet.User*, you should specify the fully-qualified class names instead (e.g. *Mail.model.User* in this case).

## The Launch Process

The launch process using Profiles is almost exactly the same as that without using Profiles. Profile-based apps have a 3-stage launch process; after all of the dependencies have been loaded, the following happens:

1. Controllers are instantiated; each Controller's {@link Ext.app.Controller#init init} function is called
2. The Profile's {@link Ext.app.Profile#launch launch} function is called
3. The Application's {@link Ext.app.Application#launch launch} function is called.

When using Profiles it is common to use the Profile launch functions to create the app's initial UI. In many cases this means the Application's launch function is completely removed, as the initial UI is usually different in each Profile (you can still specify an Application-wide launch function for setting up items such as analytics or other profile-agnostic setup).

A typical Profile launch function might look like in the following sample:

    Ext.define('Mail.profile.Phone', {
        extend: 'Ext.app.Profile',

        config: {
            name: 'Phone',
            views: ['Main']
        },

        isActive: function() {
            return Ext.os.is.Phone;
        },

        launch: function() {
            Ext.create('Mail.view.phone.Main');
        }
    });

Note that both Profile and Application launch functions are optional - if you do not define them, they are not called.

## Specializing Views

Most of the specialization implemented by a Profile occurs in the Views and the Controllers. Let us assume that we have the following tablet Profile:

    Ext.define('Mail.profile.Tablet', {
		extend: 'Ext.app.Profile',

		config: {
			views: ['Main']
		},

		launch: function() {
		    Ext.create('Mail.view.tablet.Main');
		}
	});

When this app boots on a tablet device, the file *app/views/tablet/Main.js* is loaded as usual. The following code sample is the content of the *app/views/tablet/Main.js* file:

	Ext.define('Mail.view.tablet.Main', {
		extend: 'Mail.view.Main',

		config: {
			title: 'Tablet-specific version'
		}
	});

Usually, when we define a view class we extend one of Sencha Touch's built in views, but in the previous example we extend Mail.view.Main, one of our own views. The following code sample illustrates how Mail.view.Main looks like:

    Ext.define('Mail.view.Main', {
        extend: 'Ext.Panel',

        config: {
            title: 'Generic version',
            html: 'This is the main screen'
        }
    });

We thus have a superclass (Mail.view.Main) and a Profile-specific subclass (Mail.view.tablet.Main), which can customize any aspect of the superclass. In this case we change the title of the Main view from "Generic version" to "Tablet-specific version" in our subclass, so that is what we see when the app loads.

Since these are normal classes, it is easy to customize almost any part of the superclass using the flexible config system. For example, assuming that we also have a phone version of the app, we could customize its version of the Main view as follows (*app/view/phone/Main.js*):

    Ext.define('Mail.view.phone.Main', {
		extend: 'Mail.view.Main',

		config: {
			title: 'Phone-specific version',

			items: [
			    {
			        xtype: 'button',
			        text: 'This is a phone...'
			    }
			]
		}
	});

### Sharing Sub-Views

While the previous example is useful, it is more common to share certain pieces of views and stitch them together in different ways for different profiles. For example, imagine an email app where the tablet UI is a split screen with a message list on the left and the current message loaded on the right. The Phone version is the exact same message list and a similar message view, but this time in a {@link Ext.layout.Card card layout}, since there is not enough screen space to display both views simultaneously.

To achieve this we have to create the two shared sub-views - the message list and the message viewer. In each case we have left the class config out for brevity:

    Ext.define('Mail.view.MessageList', {
        extend: 'Ext.List',
        xtype: 'messagelist'

        // config goes here...
    });

And the Message Viewer:

    Ext.define('Mail.view.MessageViewer', {
        extend: 'Ext.Panel',
        xtype: 'messageviewer'

        // config goes here...
    });

Eventually, in order to achieve the target layout that contains two views, the tablet Main view might use the following code :

    Ext.define('Mail.view.tablet.Main', {
        extend: 'Ext.Container',

        config: {
            layout: 'fit',
            items: [
                {
                    xtype: 'messagelist',
                    width: 200,
                    docked: 'left'
                },
                {
                    xtype: 'messageviewer'
                }
            ]
        }
    });

This creates a 200px wide messagelist on the left, and uses the rest of the device's screen space to show the message viewer. Now let us assume we wanted to achieve the Phone layout:

    Ext.define('Mail.view.phone.Main', {
        extend: 'Ext.Container',

        config: {
            layout: 'card',
            items: [
                {
                    xtype: 'messagelist'
                },
                {
                    xtype: 'messageviewer'
                }
            ]
        }
    });

In this case we use a Container with a {@link Ext.layout.Card card layout} (a layout that only shows one item at a time), and put both the list and the viewer into it. In this case we still need to add in some logic that tells the Container to show the messageviewer when a message in the list is tapped on, but we have easily reused our two sub-views in different configurations based on the currently loaded Profile.

Similar to the previous example, we have the option to customize the two shared views for each Profile - for example we could create the *Mail.view.phone.MessageViewer* and the *Mail.view.tablet.MessageViewer* subclasses, both of which extend the *Mail.view.MessageViewer* superclass. This enables us to again share a lot of view code between those classes, while presenting customizations appropriate for the actual device used.

## Specializing Controllers

Similar to Views, many applications have some Controller logic that can be shared across multiple Profiles. In the case of Profiles, the most important differences are usually workflow-related. For example, an app's tablet profile may allow you to complete a workflow on a single page, whereas the phone profile presents a multi-page wizard.

In the following example we have a simple Phone profile that loads a view called *Main* and a controller called *Messages*. As before, this app loads *app/view/phone/Main.js* and *app/controller/phone/Messages.js*:

    Ext.define('Mail.profile.Phone', {
		extend: 'Ext.app.Profile',

		config: {
			views: ['Main'],
			controllers: ['Messages']
		},

		launch: function() {
		    Ext.create('Mail.view.phone.Main');
		}
	});

Since we already know that our phone and tablet-specific controllers share most of their functionality, we create a controller superclass in *app/controller/Messages.js*:

    Ext.define('Mail.controller.Messages', {
        extend: 'Ext.app.Controller',

        config: {
            refs: {
                viewer: 'messageviewer',
                messageList: 'messagelist'
            },
            control: {
                messageList: {
                    itemtap: 'loadMessage'
                }
            }
        },

        loadMessage: function(item) {
            this.getViewer().load(item);
        }
    });

The Controller from the previous example performs three operations:

1. Sets up {@link Ext.app.Controller#refs refs} referencing views that we care about.
2. Listens for the `itemtap` event on the message list and calls the `loadMessage()` function when `itemtap` is fired.
3. Loads the selected message item into the Viewer when `loadMessage()` is called.

We can now create the phone-specific Controller as follows:

    Ext.define('Mail.controller.phone.Messages', {
        extend: 'Mail.controller.Messages',

        config: {
            refs: {
                main: '#mainPanel'
            }
        },

        loadMessage: function(item) {
            this.callParent(arguments);
            this.getMain().setActiveItem(1);
        }
    });

This code extends the Messages superclass controller and provides the following functionality:

1. Adds another ref for the phone UI's main panel
2. Extends the `loadMessage` function to perform the original logic and then sets the main panel's active item to the message viewer.

The entire superclass configuration is inherited by the subclass that extends it. In the case of duplicated configs such as {@link Ext.app.Controller#refs refs}, the configuration is merged, so the phone Messages controller class has three refs - main, viewer, and messageList. As with any class that extends another class, we can use `callParent` to extend an existing function in the superclass.

Remember that the *Mail.controller.Messages* superclass is not declared as a dependency by either the Application or the Profile. It it automatically loaded because our *Mail.controller.phone.Messages* controller extends it.

### What Code to Share

In the previous example we were able to share some (but not all) of our {@link Ext.app.Controller#refs refs}. We were also able to share the single event that we listen for by using the Controller's {@link Ext.app.Controller#cfg-control control} config. Generally speaking, the more an app diverges between profiles, the fewer refs and control configs you will be able to share.

One Controller config that should be shared across profiles is {@link Ext.app.Controller#routes routes}. These config maps URLs to Controller actions and provides back button support and deep linking. It is important to keep the routes config in the superclass, because the same URL should map to the same content regardless of the device.

For example, if a friend is using the phone version of your app and sends you a link to the app page she is currently on, you should be able to tap that link on your tablet device and see the tablet-specific view for that URL. Keeping all routes in the superclass enables you to keep a consistent URL structure that works regardless of device.

## Specializing Models

Models are customized per Profile less frequently than Controllers and Views, so that do not usually require a subclass. In this case we only specify the fully qualified class names for models:

	Ext.define('Mail.profile.Tablet', {
		extend: 'Ext.app.Profile',

		config: {
			models: ['Mail.model.Group']
		}
	});
