# Getting Started with Sencha Touch

## What is Sencha Touch?

Sencha Touch enables you to quickly and easily create HTML5 based mobile apps that work on Android, iOS, and Blackberry devices, and produce a native-app-like experience inside a browser or in a hybrid shell.

## Prerequisites

First, you need to download the free [Sencha Touch SDK](http://www.sencha.com/products/touch/download/) and
[Sencha Cmd](http://www.sencha.com/products/sencha-cmd/download) from the Sencha website. You will also need the following:

 - A web server running locally on your computer
 - A modern web browser; [Chrome](https://www.google.com/chrome) and [Safari](http://www.apple.com/safari/download/) are recommended

 If you are running the IIS web server on Windows, please note that you have to manually add `application/x-json` as a MIME Type for Sencha Touch to work properly. For information on adding this MIME type see the following link: [http://stackoverflow.com/a/1121114/273985](http://stackoverflow.com/a/1121114/273985).

## Installation

Extract the SDK zip file to your projects directory. Ideally, this folder will be accessible to your HTTP server. For example, you should be able to navigate to http://localhost/sencha-touch-2.1.1 from your web browser and see the Sencha Touch documentation.

You also need to run the Sencha Cmd installer. The installer adds the **`sencha`** command line tool to your path, enabling you to generate a fresh application template, among other things. To check that you have correctly installed Sencha Cmd, change to your Sencha Touch directory and type in the `sencha` command, as shown, for example, in the following code sample:

    $ cd ~/webroot/sencha-touch-2.1.0-gpl/
    $ sencha
    Sencha Cmd v3.0.0
    ...

__Note:__ When using the **`sencha`** command, you **must** be inside either the downloaded SDK directory or a generated Touch app. For further details see the [Sencha Cmd](#!/guide/command) documentation.

## Generating Your First App

Now that you have Sencha Touch and Sencha Cmd installed, you can generate an application. While still in the
Sencha Touch SDK folder, type in the following:

    $ sencha generate app MyApp ../MyApp
    [INFO] Created file ...
    ...

This generates a skeleton Sencha Touch application namespaced to the `MyApp` variable and located in the `../MyApp` directory (one level up from the Sencha Touch SDK directory). The skeleton app contains all the files you need to create a Sencha Touch application, including the default index.html file, a copy of the Touch SDK, the CSS file, images and configuration files for creating native packages for your app.

You can verify if your application has generated successfully by opening it in a web browser. Assuming that you extracted the SDK to your webroot folder, you should be able to navigate to `http://localhost/MyApp` and see the default app as shown in the following image.


## Explore the Code

When you open the MyApp directory in your favorite IDE or text editor, you notice that the directory structure looks like the following image:

The following listing provides a short description of each file and directory, the complete list of the generated files can be found in the [Sencha Cmd](#!/guide/command) documentation:

  - **`app`** - The directory containing the Models, Views, Controllers, and Stores for your app.
  - **`app.js`** - The main JavaScript entry point for your app.
  - **`app.json`** - The configuration file for your app. 
  - **`index.html`** - The HTML file for your app.
  - **`packager.json`** - The configuration file used by Sencha Cmd for creating native packages for your application.
  - **`resources`** - The directory containing the CSS and the images for your app

Open `app.js`, the main entry point for your app, in your editor:

The `launch` function is the entry point to your application. In the default application, we first hide the application loading indicator, then we create an instance of our Main view and add it to the Viewport.

The Viewport is a {@link Ext.layout.Card Card layout} to which you can add application components. The default app adds the `Main` view to the viewport so it becomes visible on the screen. 

Look at the code inside the Main view.

Open `app/view/Main.js` in your code editor and change a title line to:

    title: 'Home Tab'

Then change another line as follows:

    title: 'Woohoo!'

Also, change lines as follows:

    html: [
        "I changed the default <b>HTML Contents</b> to something different!"
    ].join("")

Refresh the app in your browser to see the effects of your changes.


## Next Steps

The next step is to follow the [First Application guide](#!/guide/first_app), which builds on the operations from this guide, and which guides your through creating a simple but powerful app in around 15 minutes. You can also follow along with the video at the start of this guide.

If you would like to skip ahead or find out more detailed information about other aspects of the framework, we recommend referring to the following guides and resources:

### Guides

* [Components and Containers](#!/guide/components)
* [Intro to Applications](#!/guide/apps_intro)
* [The Layout System](#!/guide/layouts)
* [The Data Package](#!/guide/data)
* [What's New in Sencha Touch 2](#!/guide/whats_new)

### Application Examples

* [Kitchen Sink](#!/example/kitchen-sink)
* [Twitter](#!/example/twitter)
* [Kiva](#!/example/kiva)

### Component Examples

* [Carousel](#!/example/carousel)
* [Forms](#!/example/forms)
* [Date Picker](#!/example/pickers)
