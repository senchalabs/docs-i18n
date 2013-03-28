# Using and Creating Builds

Sencha Touch comes with a class system that features the ability to dynamically load classes when they are needed. This approach has many benefits in both development and production.

In development, dynamic loading means that you get a file-by-file stack trace, which makes it much easier to debug problems with your application. For production, we provide a build tool that enables you to easily create a minified custom build that only includes the classes your app actually uses, meaning loading times are often reduced for your users.

## Choosing a Build

Sencha Touch ships with five builds out of the box. If you want to get up and running as quickly as possible, it is best to simply use sencha-touch-debug.js while developing your app locally, then switch to sencha-touch.js in production. The other three builds are good for debugging in production, running in production without a custom build, and migrating your 1.x app to 2.x.

Because each build is used for a different purpose and is created using a different set of build options, we have created a simple table that details the configuration of each one:

<style type="text/css" media="screen">
    .guide-container table {
        width: 900px;
        font-size: 0.9em;
    }

    .guide-container table th {
        background-color: #eee;
        font-weight: bold;
        text-align: center;
        color: #333;
        padding: 1px 2px;
    }
    
    .guide-container table td {
        padding: 3px;
    }
</style>

<table class="info">
    <tr>
        <th>Name</th>
        <th>Type</th>
        <th>Loader</th>
        <th>Minified</th>
        <th>Comments</th>
        <th>Debug</th>
        <th>Compat</th>
        <th>Usage</th>
    </tr>
    <tr>
        <th>sencha-touch-debug.js</th>
        <td>Core</td>
        <td>{@img tick.png}</td>
        <td></td>
        <td>{@img tick.png}</td>
        <td>{@img tick.png}</td>
        <td></td>
        <td>Use when developing your app locally</td>
    </tr>
    <tr>
        <th>sencha-touch.js</th>
        <td>Core</td>
        <td></td>
        <td>{@img tick.png}</td>
        <td></td>
        <td></td>
        <td></td>
        <td>Use in production with a custom build</td>
    </tr>
    <tr>
        <th>builds/sencha-touch-all.js</th>
        <td>All</td>
        <td></td>
        <td>{@img tick.png}</td>
        <td></td>
        <td></td>
        <td></td>
        <td>Use in production if you don't have a custom build</td>
    </tr>
    <tr>
        <th>builds/sencha-touch-all-debug.js</th>
        <td>All</td>
        <td></td>
        <td></td>
        <td>{@img tick.png}</td>
        <td>{@img tick.png}</td>
        <td></td>
        <td>Use to debug your app in staging/production</td>
    </tr>
    <tr>
        <th>builds/sencha-touch-all-compat.js</th>
        <td>All</td>
        <td></td>
        <td></td>
        <td>{@img tick.png}</td>
        <td>{@img tick.png}</td>
        <td>{@img tick.png}</td>
        <td>Use to migrate your 1.x app to 2.x</td>
    </tr>
</table>

Note that the last 3 builds are contained within the 'builds' directory in the SDK download. If the table above does not provide sufficient information, the following list provides more detail on the meaning of each option:

<ul>
    <li><strong>Type</strong>: either "Core" or "All" - Core includes the base classes but none of the Components, All means everything is included</li>
    <li><strong>Loader</strong>: whether dynamic loading is activated or not. Only sencha-touch-debug.js has this activated by default</li>
    <li><strong>Minified</strong>: means the build has been compressed with YUI compressor</li>
    <li><strong>Comments</strong>: means the build still contains the JSDoc comments (these are usually stripped in production to speed up downloads)</li>
    <li><strong>Debug</strong>: means that the build will give you debug messages, such as indicating if you have misconfigured a class</li>
    <li><strong>Compat</strong>: means that code to provide backwards compatibility with Sencha Touch 1.x is present in the build</li>
</ul>

Again, use sencha-touch-debug.js in development mode, then in production switch to either sencha-touch.js or sencha-touch-all.js plus a custom build.

## Creating Your Own Build

In the vast majority of cases a Sencha Touch app should use a custom build in production, for two main reasons:

1. Custom builds include **only** the framework classes that your app is actually using, saving on download time
2. A custom build includes all of your app classes in a single file, which translates into a single network request

For deployed applications, the second reason is most important. Since most applications have a large number of files (sometimes hundreds), loading them one by one, especially over a mobile connection, can take a long time. Each request can add several hundred milliseconds of delay which can easily add several seconds to your application's overall load time.

To build your applications to load quickly in a production environment, use Sencha's command line build tool, <a href="http://www.sencha.com/products/sencha-cmd/download">Sencha Cmd</a>, includes functionality that:

1. Figures out which framework classes your application uses
2. Figures out which application classes are loaded when your application starts
3. Combines all of these into a single file, with the classes in the correct order
4. Strips out all of the JSDoc comments, then minifies the file so it is as small as possible

### Installing Sencha Cmd

If you do not already have Sencha Cmd installed, visit the [Sencha Cmd](#!/guide/command) guide to set up your development environment to run Sencha Cmd.

### Generating a Build

We are going to assume that you have an app that already works locally and that you just want to build it for production. If you do not have an app yet or do not know how to create one, check out the <a href="#!/guide/apps_intro">getting started guide</a>.

Assuming that your app does work locally, let us proceed. In order to illustrate how this works, we are going to use the Twitter example that comes with the Sencha Touch SDK. Let us start by taking a look at the example's index.html file:

    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>Twitter</title>

        <link rel="stylesheet" href="resources/css/application.css" type="text/css">

        <script type="text/javascript" src="touch/sencha-touch-debug.js"></script>
        <script type="text/javascript" src="app.js"></script>
    </head>
    <body></body>
    </html>

Note that we are loading sencha-touch-debug.js and app.js, a combination which allows us to use dynamic loading while developing our app, and which is the basis for the Sencha Cmd's ability to generate a minimal build. We will come back to this HTML file's contents shortly.

At the command line, you will first need to cd to the hard drive directory that contains your app:

    cd ~/path/to/my/app

Next you will need to generate a jsb file for your app. A jsb file is basically a list of all the classes that your application uses. To do this, use Sencha Cmd as follows:

    sencha create jsb -a index.html -p app.jsb3

This command takes your index.html file (the same file you use in your browser while developing your app), figures out all of the class dependencies and writes them out into a file called *app.jsb3*. At this step you need one more command to actually generate the build itself:

    sencha build -p app.jsb3 -d ./

This final command takes all files listed in the jsb and combines them into a single file, which it then minifies in order to make it as small as possible. The output is a file called all-classes.js, which contains all of the framework classes plus your application classes. 

### Updating your HTML file

The final step for preparing your app for production is to update your HTML file to use sencha-touch.js instead of sencha-touch-debug.js, and to load your newly-generated all-classes.js file. The twitter example file ends up as follows:

    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>Twitter</title>

        <link rel="stylesheet" href="resources/css/application.css" type="text/css">

        <script type="text/javascript" src="touch/sencha-touch.js"></script>
    	<script type="text/javascript" src="all-classes.js"></script>
        <script type="text/javascript" src="app.js"></script>
    </head>
    <body></body>
    </html>

Rather than change your main index.html file all the time, it is common to create a duplicate called index-production.html that looks like the previous file. Many developers produce a simple deploy script that automatically copies the app into a deploy folder and rename index-production.html to index.html, such that the build can be uploaded.
