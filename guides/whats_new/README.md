# What's New in Sencha Touch 2.1

The following is a list of the new features and functionalities in Sencha Touch 2.1 that were not part of earlier versions. 

## Welcome to Sencha Touch 2.1

Sencha Touch was the first HTML5 mobile framework and 2.x is its most significant upgrade. The foremost focus of the release is performance -- getting apps running fast and on as many devices as possible. Apps start up much faster, provide a much snappier initial rendering and layout, and redraw immediately when a device is rotated. Among the other significant enhancements are the following:

 - A new scroller, optimized for each platform and faster than ever - especially on Android devices. The rendering process has been optimized and components are reused without the app having to instantiate new ones.

 - Many innovations that originate from Ext JS 4, including the new advanced class system, reconfigurable components, and improvements to the application architecture.

 - Increased support for a wider range of devices with reduced emphasis on WebKit, and a more robust platform that can be used to support more devices over time. 

Here is a closer look at these and other enhancements introduced with Sencha Touch 2.1.

## Smaller, faster layout engine

Sencha Touch offers a very flexible layout system that makes it easy to lay out apps for a variety of device shapes and sizes. Version 2.1 provides a layout engine that runs much more like the browser's optimized CSS engine, the result being enormously improved performance in several key areas:

* On startup apps render and lay out faster 
* After rotating the device, updating the screen is much faster than with Sencha Touch 1.x apps
* The layout engine is much smaller, resulting in faster download

The result is a massively improved layout performance across all areas. Screens are rendered much faster when navigating through the app, giving a much more fluid experience. The most dramatic improvement occurs when a device changes orientation. To see the impact of the new layout engine, here is a video of the Kitchen Sink buttons example running on Sencha Touch 1.x and 2.x respectively, slowed down to one quarter of its normal speed:

<a style="text-align: center" href="http://vimeo.com/30296006"><img src="http://img1.sencha.com/files/misc/20111010-video-full.jpg" /></a>

## Stronger, smarter core

Sencha Touch benefits from a development environment that is shared with Ext JS. The Ext JS 4 environment brought about a range of new innovations that are now also build into Sencha Touch, including the following:

* Upgraded class system with support for dynamic loading and dependencies
* Support for class configurations as a core construct, which provides you with free getter and setter functions and results in a clean, consistent API

### Faster startup time

Sencha Touch 2 starts faster than previous versions, providing nearly a 10% to 25% improvement in startup time on a range of devices, as shown when testing the Kitchen Sink example. On many devices, version 2.x apps load almost a second faster than previous version apps:

<img src="http://img1.sencha.com/files/misc/20111010-sencha-touch-startup-times.png" />

## Class system and apps

Sencha Touch 2.1 uses the powerful new class system derived from Ext JS 4. This provides all the benefits of dynamic loading, intelligent builds that only include the classes you use, mixins, configurations, and all the other features of the new engine. For details, see the [How to use classes in Sencha Touch 2](#!/guide/class_system) guide.

Sencha Touch 2.1 also includes the Ext JS 4 application architecture, including ComponentQuery and production build support. 

### Config-driven components

One of the benefits of the new class system is the ability to set up 'configs', simple properties that are automatically given getter and setter functions, defaults, and more.

Sencha Touch 2.1 makes use of the config system throughout the framework. Whenever you see a config on a class, you already know that you can reconfigure it at any time (even after it is rendered). Even better, because the config's setter name always follows the same pattern, you already know what function to call.

For example, we can give a {@link Ext.form.Text Text Field} a label when we instantiate it and then know we can easily change it later on:

	var text = Ext.create('Ext.form.Text', {
		label: 'My Field'
	});
	
	//anything we can configure also has a setter function
	//its name always follows the setConfigName pattern
	text.setLabel('Another Field');

Configs are an important enhancement because they give classes a very clean API. For each class, everything you see in the 'Config options' section in the API documentation is a true config, complete with standardized getter and setter functions.

For a full overview of the new capabilities see the <a href="#!/guide/class_system">Class System guide</a>.

## Improved MVC Capabilities

Sencha Touch 1 provided a simple way to organize your application based on the Model View Controller (MVC) paradigm. Sencha Touch 2 significantly improves on this functionality, incorporating full history support, a powerful new way to control Components, and a powerful way to customize your application for a different screen sizes.

In addition to this, the data package has been ported to use the new class system, making it much more flexible and improving performance. For a full overview on the MVC improvements in Sencha Touch 2.1 see the following guides:

* <a href="#!/guide/apps_intro">Intro to Applications</a>
* <a href="#!/guide/profiles">Profiles</a>
* <a href="#!/guide/controllers">Controllers</a>
* <a href="#!/guide/history_support">History Support</a>

## Much better Android support

Sencha Touch 2.1 brings a big improvement in Android performance, particularly when it comes to scrolling and animation. In Sencha Touch 1.x, Android devices were noticeably slower when scrolling through large lists, while animations could be choppy and exhibit weird visual artifacts.

The Sencha Touch 2.1 framework gives Android its own optimized mechanism for achieving both smooth scrolling and fast, fluid animations, as illustrated by the following video that demonstrates current performance on Android devices:

<a style="text-align: center" href="http://vimeo.com/30324079"><img src="http://img1.sencha.com/files/misc/20111010-video-sm.jpg" /></a>

## Native packaging

One of the most common questions developers ask when building Sencha Touch apps is, “How do I get my app in front of customers?” In many scenarios, building and deploying apps to the web is exactly what developers want and what customers expect. In other cases, getting apps into application stores is the fastest way to reach customers.

Sencha Touch 2.1 makes it incredibly easy to build and deploy apps to both the Google Play store and the Apple App Store. The Sencha Cmd utility, which is available both on Mac OS X and Windows, provides the packaging capabilities as well as new APIs to access native device functionality. For all the details on how to use the new packaging capabilities, see [Native Packaging guides](#!/guide/native_packaging).

## Overhauled docs

All of the most widely used classes in Sencha Touch 2.1 feature excellent documentation right in the API reference documentation. Interspersed into the class docs are dozens of live examples that run from within your browser and let you see (and even modify) the example code on the fly. We have also brought all of the Sass variables for each component into the API docs, making it much easier to see the items that you can customize.

Sencha Touch's [guides](#!/guide) have been updated and refreshed. We currenly have guides that explain core concepts such as layouts, components, and classes, and other guides that cover how to use components such as tab panels, forms, and carousels. The documentation also includes a new Getting Started guide that takes you through building your first app from scratch.