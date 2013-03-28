# Using Views in Your Applications

From a user's point of view, your application is simply a collection of views. Although much of the value of the app is in the Models and Controllers, users directly interact with the Views. In this guide we are going to look at how to create the views that build your application.

## Using Existing Components

The easiest way to create a view is to use Ext.create with an existing Component. For example, if we wanted to create a simple Panel with some HTML inside, we can use the following code:

    @example
    Ext.create('Ext.Panel', {
        html: 'Welcome to my app',
        fullscreen: true
    });

This example creates a Panel with some html content and makes it fill the screen. Although you can create any of the built-in components this way, the best practice is to create a subclass with your specializations and then instantiate that class as follows:

    @example
    Ext.define('MyApp.view.Welcome', {
        extend: 'Ext.Panel',
        
        config: {
            html: 'Welcome to my app',
            fullscreen: true
        }
    });
    
    Ext.create('MyApp.view.Welcome');

The outcome is the same, but this time we have a new component that we can create any number of times. This is the pattern we normally want to follow when building our app - create a subclass of an existing component, then create an instance of it later. Let us take a look at the changes:

* We used Ext.define for creating a new class, by extending an existing one (Ext.Panel in this case)
* We followed the MyApp.view.MyView convention for our new view class. Although you can name it whatever you like,  we suggest sticking with this naming convention
* We defined config for the new class inside a config object

Any of the config options available on Ext.Panel can be specified in either our new class's config block or when we come to create the class instance. When defining a subclass make sure to use the config object, when creating just pass in an object.

For example, the following code is based on the previous code, but with additional configuration passed in with the Ext.create call:

    @example
    Ext.define('MyApp.view.Welcome', {
        extend: 'Ext.Panel',
        
        config: {
            html: 'Welcome to my app',
            fullscreen: true
        }
    });
    
    Ext.create('MyApp.view.Welcome', {
        styleHtmlContent: true
    });

## A Real World Example

The following exmple illustrates one of the View classes from our Twitter app:

    Ext.define('Twitter.view.SearchBar', {
        extend: 'Ext.Toolbar',
        xtype : 'searchbar',
        requires: ['Ext.field.Search'],

        config: {
            ui: 'searchbar',
            layout: 'vbox',
            cls: 'big',

            items: [
                {
                    xtype: 'title',
                    title: 'Twitter Search'
                },
                {
                    xtype: 'searchfield',
                    placeHolder: 'Search...'
                }
            ]
        }
    });

This code sample follows the same pattern as before - we created a new class called Twitter.view.SearchBar, which extends the framework's Ext.Toolbar class. We also passed in some configuration options, including a layout and an {@link Ext.Container#cfg-items items} array.

In this example we used the following new options:

* **requires** - because we use a searchfield in our items array, we indicate the new view to require the Ext.field.Search class. At the moment the dynamic loading system does not recognize classes specified by xtype, so we need to define the dependency manually
* **xtype** - gives our new class its own xtype, allowing us to easily create it in a configuration object, which is similar to the approach taken with the searchfield

This allows us to create instances of our new view class in a couple of ways:

    //creates a standalone instance
    Ext.create('Twitter.view.SearchBar');
    
    //alternatively, use xtype to create our new class inside a Panel
    Ext.create('Ext.Panel', {
        html: 'Welcome to my app',
        
        items: [
            {
                xtype: 'searchbar',
                docked: 'top'
            }
        ]
    });

## Custom Configurations and Behavior

Sencha Touch makes extensive use of the configuration system to provide predictable APIs and keep the code clean and easily testable. We strongly suggest you do the same in your own classes.

Let us assume that you want to create an image viewer that pops up information about the image when you tap on it. Our design goal is to create a reusable view that can be configured with the image url, its title and its description, and that displays the title and description when you tap on it.

Most of the work around displaying images is taken care of for us by the Ext.Img component, so we subclass that as follows:

    @example
    Ext.define('MyApp.view.Image', {
        extend: 'Ext.Img',
        
        config: {
            title: null,
            description: null
        },
        
        //sets up our tap event listener
        initialize: function() {
            this.callParent(arguments);
            
            this.element.on('tap', this.onTap, this);
        },
        
        //this function is called whenever you tap on the image
        onTap: function() {
            Ext.Msg.alert(this.getTitle(), this.getDescription());
        }
    });
    
    //creates a full screen tappable image
    Ext.create('MyApp.view.Image', {
        title: 'Orion Nebula',
        description: 'The Orion Nebula is rather pretty',
        
        src: 'http://apod.nasa.gov/apod/image/1202/oriondeep_andreo_960.jpg',
        fullscreen: true
    });

In this example, we have added two additional configurations to our class - title and description - which both start off as null. When we create an instance of our new class, we pass in the title and the description configs like any other configuration.

The new behavior occurs in the initialize and onTap functions. Since the initialize function is called whenever any component is instantiated, it is a good place to set up behavior such as event listeners. First we use this.callParent(arguments) to make sure the superclass' initialize function is called. **This is very important**, omitting this line may cause your components not to behave correctly.

After callParent, we add a tap listener to the component's element, which calls the onTap function whenever the element is tapped. All components in Sencha Touch have an element property that you can use in this way to listen to events on the DOM objects, to add or remove styling, or to do anything else you would normally do to an Ext.dom.Element.

The onTap function itself uses Ext.Msg.alert to pop up some information about the image. Note that our two new configs - title and description - both receive generated getter functions (getTitle and getDescription respectively), as well as generated setter functions (setTitle and setDescription).

## Advanced Configurations

When you create a new configuration option to a class, the getter and setter functions are generated for you. For example, the config called 'border' in the following example is automatically given getBorder and setBorder functions:

    @example
    Ext.define('MyApp.view.MyView', {
        extend: 'Ext.Panel',
        
        config: {
            border: 10
        }
    });
    
    var view = Ext.create('MyApp.view.MyView');
    
    alert(view.getBorder()); //alerts 10
    
    view.setBorder(15);
    alert(view.getBorder()); //now alerts 15

The getter and setter are not the only generated functions, there are a couple more that make life as a component author much simpler - applyBorder and updateBorder:

    Ext.define('MyApp.view.MyView', {
        extend: 'Ext.Panel',
        
        config: {
            border: 0
        },
        
        applyBorder: function(value) {
            return value + "px solid red";
        },
        
        updateBorder: function(newValue, oldValue) {
            this.element.setStyle('border', newValue);
        }
    });

The applyBorder function is called internally any time the border configuration is set or changed, including when the component is first instantiated. This is the best place to put any code that transforms an input value. In this case we take the border width passed in and return a CSS border specification string.

This means that when we set the view's border config to 10, our applyBorder function will make sure that we transform that value to '10px solid red'. The apply function is optional, but note that you must **return** a value from it or nothing will happen.

The updateBorder function is called *after* the applyBorder function has transformed the value, and is usually used to modify the DOM, to send AJAX requests, or to perform any other kind of processing. In our case we are getting the view's element and updating the border style using {@link Ext.dom.Element#setStyle setStyle}. This means that every time setBorder is called, the DOM is immediately updated to reflect the new style.

The following code is an example of the new view in action. Click the Code Editor button to see the source - basically we created an instance of the new view and docked a {@link Ext.field.Spinner spinner field} at the top, allowing us to change the border width by tapping the spinner buttons. We then hooked into the Spinner's {@link Ext.field.Spinner#event-spin spin} event and called our view's new setBorder function from there:

    @example preview
    //as before
    Ext.define('MyApp.view.MyView', {
        extend: 'Ext.Panel',
        
        config: {
            border: 0
        },
        
        applyBorder: function(value) {
            return value + "px solid red";
        },
        
        updateBorder: function(newValue, oldValue) {
            this.element.setStyle('border', newValue);
        }
    });
    
    //create an instance of MyView with a spinner field that updates the border config
    var view = Ext.create('MyApp.view.MyView', {
        border: 5,
        fullscreen: true,
        styleHtmlContent: true,
        html: 'Tap the spinner to change the border config option',
        items: {
            xtype: 'spinnerfield',
            label: 'Border size',
            docked: 'top',
            value: 5,
            minValue: 0,
            maxValue: 100,
            increment: 1,
            listeners: {
                spin: function(spinner, value) {
                    view.setBorder(value);
                }
            }
        }
    });

## Usage in MVC

We recommend that most Sencha Touch applications follow the <a href="#!/guide/apps_intro">MVC conventions</a> so that your code remains well organized and reusable. As the "V" in MVC, Views also fit into this structure. The conventions around Views in MVC are very simple and follow directly from the naming convention we used throughout the previous examples.

The *MyApp.view.MyView* class should be defined inside a file called *app/view/MyView.js* - this allows the Application to find and load it automatically. If you are not already familiar with the file structure for MVC-based Sencha Touch apps, it is pretty simple - an app is just an html file, an *app.js* file, and a collection of models, views and controllers stored inside the *app/model*, *app/view* and *app/controller* directories:

    index.html
    app.js
    app/
        controller/
        model/
        view/
            MyView.js

You can create as many views as you want and organize them inside your *app/view* directory. By specifying your application's Views inside your app.js file, they are loaded automatically:

    //contents of app.js
    Ext.application({
        name: 'MyApp',
        views: ['MyView'],
        
        launch: function() {
            Ext.create('MyApp.view.MyView');
        }
    });

By following the simple View naming conventions, you can easily load and create instances of View classes inside your application. The previous example does exactly that - it loads the MyView class and creates an instance of it in the application launch function. To find out more about writing MVC apps in Sencha Touch please see the <a href="#!/guide/apps_intro">intro to apps guide</a>.