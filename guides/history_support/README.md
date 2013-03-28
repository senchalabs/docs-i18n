# Routing, Deep Linking, and the Back Button

Sencha Touch comes with fully history and deep-linking support. This gives your web applications the following two important benefits:

* The back button works inside your apps, helping you to navigate correctly and quickly between screens without refreshing the page
* Deep-linking enables your users to send a link to any part of the app and have other load the right page

The result is an application that feels much more in tune with what users expect from native apps, especially on Android devices which fully support the built-in back button.

## Setting Up Routes

Setting up history support for your apps is pretty straightforward and is centered around the concept of routes. Routes are a simple mapping between urls and controller actions: whenever a certain type of url is detected in the address bar, the corresponding Controller action is called automatically. Let us take a look at a simple Controller:

    Ext.define('MyApp.controller.Products', {
        extend: 'Ext.app.Controller',

        config: {
            routes: {
                'products/:id': 'showProduct'
            }
        },

        showProduct: function(id) {
            console.log('showing product ' + id);
        }
    });

By specifying the {@link Ext.app.Controller#routes routes} in the previous code sample, the Main controller is notified whenever the browser url looks like "#products/123". For example, if your application is deployed onto http://myapp.com, any url that looks like http://myapp.com/#products/123, http://myapp.com/#products/456, or http://myapp.com/#products/abc, automatically causes the showProduct function to be called.

When the showProduct function is called this way, it is passed the 'id' token that was parsed from the url. This happens because we used ':id' in the route - whenever a route contains a ':' character, it attempts to pull that information from the url and pass it into the called function. Note that these parsed tokens are always strings (because urls  themselves are always strings), so hitting a route like 'http://myapp.com/#products/456' is the same as calling `showProduct('456')`.

You can specify any number of routes and your routes can each have any number of tokens, as shown in the following example:

    Ext.define('MyApp.controller.Products', {
        extend: 'Ext.app.Controller',

        config: {
            routes: {
                'products/:id': 'showProduct',
                'products/:id/:format': 'showProductInFormat'
            }
        },

        showProduct: function(id) {
            console.log('showing product ' + id);
        },

        showProductInFormat: function(id, format) {
            console.log('showing product ' + id + ' in ' + format + ' format');
        }
    });

The second route accepts urls such as #products/123/pdf, which route through to the showProductInFormat function and console log 'showing product 123 in pdf format'. Note that the arguments are passed into the function in the order in which they appear in the route definition.

Of course, your Controller function probably will not just log a message to the console, it can do anything needed by your app - fetching data, updating the UI, or anything else.

## Advanced Routes

By default, wildcards in routes match any sequence of letters and numbers. This means that a route for "products/:id/edit" would match the url "#products/123/edit" but not "#products/a ,fd.sd/edit" - this is because the second contains a number of letters that do not qualify (space, comma, dot).

Sometimes though we want the route to be able to match urls such as these, for example if an url contains a file name, we may want to be able to pull that out into a single token. To achieve this, we can pass a configuration object into our {@link Ext.app.Route Route}:

    Ext.define('MyApp.controller.Products', {
        extend: 'Ext.app.Controller',

        config: {
            routes: {
                'file/:filename': {
                    action: 'showFile',
                    conditions: {
                        ':filename': "[0-9a-zA-Z\.]+"
                    }
                }
            }
        },

        //opens a new window to show the file
        showFile: function(filename) {
            window.open(filename);
        }
    });

In this example, instead of an action string we have a configuration object that contains an 'action' property. In addition, we added a {@link Ext.app.Route#conditions conditions} configuration which tells the :filename token to match any sequence of numbers and letters, including a period ('.'). This means our route will now match urls such as http://myapp.com/#file/someFile.jpg, passing 'someFile.jpg' in as the argument to the Controller's `showFile` function.

## Restoring State

One challenge that comes with supporting history and deep linking is that you need to be able to restore the full UI state of the app, in order to make it as if users navigated to the deep-linked page themselves. This can sometimes be tricky, but it is the price paid for making life better for the user.

Let us take the simple example of loading a product based on an url such as http://myapp.com/#products/123 and let us update the Products Controller from the previous example:

    Ext.define('MyApp.controller.Products', {
        extend: 'Ext.app.Controller',

        config: {
            refs: {
                main: '#mainView'
            },

            routes: {
                'products/:id': 'showProduct'
            }
        },

        /**
         * Endpoint for 'products/:id' routes. Adds a product details view (xtype = productview)
         * into the main view of the app then loads the Product into the view
         *
         */
        showProduct: function(id) {
            var view = this.getMain().add({
                xtype: 'productview'
            });

            MyApp.model.Product.load(id, {
                success: function(product) {
                    view.setRecord(product);
                },
                failure: function() {
                    Ext.Msg.alert('Could not load Product ' + id);
                }
            });
        }
    });

In this example the 'products/:id' url endpoint results in the immediate addition of a view into our app's main view (which could be a TabPanel or other Container), then uses our Product model (MyApp.model.Product) to fetch the Product from the server. We added a callback that populates the product detail view with the freshly loaded Product. We render the UI immediately (as opposed to only rendering it when the Product has been loaded) so that we give the user visual feedback as soon as possible.

Each app will need different logic when restoring state for a deeply-linked view. For example, the Kitchen Sink sample application needs to restore the state of its NestedList navigation, as well as rendering the correct view for the given url. To see how this is accomplished in both Phone and Tablet profiles, check out the showView functions in the Kitchen Sink's app/controller/phone/Main.js and app/controller/tablet/Main.js files.

## Sharing urls across Device Profiles

In most cases you will want to share exactly the same route structure between your [Device Profiles](#!/guide/profiles). This way a user that is using your Phone version can send their current url to a friend using a Tablet and expect that their friend will be taken to the right place in the Tablet app. This generally means it is best to define your route configurations in the superclass of the Phone and Tablet-specific Controllers:

    Ext.define('MyApp.controller.Products', {
        extend: 'Ext.app.Controller',

        config: {
            routes: {
                'products/:id': 'showProduct'
            }
        }
    });

In your Phone-specific subclass you can then implement the `showProduct` function to give a Phone-specific view for the given product:

    Ext.define('MyApp.controller.phone.Products', {
        extend: 'MyApp.controller.Products',

        showProduct: function(id) {
            console.log('showing a phone-specific Product page for ' + id);
        }
    });

In your Tablet-specific subclass you can then proceed in a similar way, this time showing a tablet-specific view:

    Ext.define('MyApp.controller.tablet.Products', {
        extend: 'MyApp.controller.Products',

        showProduct: function(id) {
            console.log('showing a tablet-specific Product page for ' + id);
        }
    });

There are some exceptions to this rule, usually having to do with linking to navigation states. The Kitchen Sink example has phone and tablet specific views - although in both profiles a NestedList is used for navigation, on the Tablet, NestedList only takes up the left hand edge of the screen, while on the Phone it fills the screen. In order to make the back button work as expected on phones, each time a user navigates in the NestedList, the new url is pushed into the history, which means that the Phone-specific controller has one additional route. Check out the app/controller/phone/Main.js file for an example of this.
