# Building Your First App

## Preparation

This guide builds on the <a href="#!/guide/getting_started">Getting Started Guide</a>, which gets you set up with the SDK installation and ensures that your environment is functional. If you have not been through this guide yet, we suggest reading it first before using this guide.

## What We Are Going to Build

We are going to build a simple mobile web app that could be used for a company's mobile site. The app includes a home page, a contact form, and a simple list that fetches our recent blog posts and allows visitors to read them on a mobile device.

The sample app's code is the following (the following code is interactive):

    @example raw portrait preview
    Ext.application({
        name: 'Sencha',

        launch: function() {
            //The whole app UI lives in this tab panel
            Ext.Viewport.add({
                xtype: 'tabpanel',
                fullscreen: true,
                tabBarPosition: 'bottom',

                items: [
                    // This is the home page, just some simple html
                    {
                        title: 'Home',
                        iconCls: 'home',
                        cls: 'home',
                        html: [
                            '<img height=260 src="http://staging.sencha.com/img/sencha.png" />',
                            '<h1>Welcome to Sencha Touch</h1>',
                            "<p>Building the Getting Started app</p>",
                            '<h2>Sencha Touch 2</h2>'
                        ].join("")
                    },

                    // This is the recent blogs page. It uses a tree store to load its data from blog.json
                    {
                        xtype: 'nestedlist',
                        title: 'Blog',
                        iconCls: 'star',
                        cls: 'blog',
                        displayField: 'title',

                        store: {
                            type: 'tree',

                            fields: ['title', 'link', 'author', 'contentSnippet', 'content', {
                                name: 'leaf',
                                defaultValue: true
                            }],

                            root: {
                                leaf: false
                            },

                            proxy: {
                                type: 'jsonp',
                                url: 'https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.feedburner.com/SenchaBlog',
                                reader: {
                                    type: 'json',
                                    rootProperty: 'responseData.feed.entries'
                                }
                            }
                        },

                        detailCard: {
                            xtype: 'panel',
                            scrollable: true,
                            styleHtmlContent: true
                        },

                        listeners: {
                            itemtap: function(nestedList, list, index, element, post) {
                                this.getDetailCard().setHtml(post.get('content'));
                            }
                        }
                    },

                    // This is the contact page, which features a form and a button. The button submits the form
                    {
                        xtype: 'formpanel',
                        title: 'Contact Us',
                        iconCls: 'user',
                        url: 'contact.php',
                        layout: 'vbox',

                        items: [
                            {
                                xtype: 'fieldset',
                                title: 'Contact Us',
                                instructions: 'Email address is optional',

                                items: [
                                    {
                                        xtype: 'textfield',
                                        label: 'Name',
                                        name: 'name'
                                    },
                                    {
                                        xtype: 'emailfield',
                                        label: 'Email',
                                        name: 'email'
                                    },
                                    {
                                        xtype: 'textareafield',
                                        label: 'Message',
                                        name: 'message',
                                        height: 90
                                    }
                                ]
                            },
                            {
                                xtype: 'button',
                                text: 'Send',
                                ui: 'confirm',

                                // The handler is called when the button is tapped
                                handler: function() {

                                    // This looks up the items stack above, getting a reference to the first form it see
                                    var form = this.up('formpanel');

                                    // Sends an AJAX request with the form data to the url specified above (contact.php).
                                    // The success callback is called if we get a non-error response from the server
                                    form.submit({
                                        success: function() {
                                            // The callback function is run when the user taps the 'ok' button
                                            Ext.Msg.alert('Thank You', 'Your message has been received', function() {
                                                form.reset();
                                            });
                                        }
                                    });
                                }
                            }
                        ]
                    }
                ]
            });
        }
    });
    

## Getting Started

The first thing we do is set up our application, as outlined in the <a href="#!/guide/getting_started">Getting Started Guide</a>. Since the app uses a {@link Ext.tab.Panel tab panel} that holds the four pages, we start with creating that UI. The entry point for Sencha Touch is the launch function of an Ext.application. Consider this the `main` part of the application, and the place that contains the application logic.

    @example raw miniphone
    Ext.application({
        name: 'Sencha',

        launch: function() {
            Ext.create("Ext.tab.Panel", {
                fullscreen: true,
                items: [
                    {
                        title: 'Home',
                        iconCls: 'home',
                        html: 'Welcome'
                    }
                ]
            });
        }
    });

If you run this code in the browser (by clicking the Preview button), a {@link Ext.tab.Panel TabPanel} should appear on top of the screen. Since the home page could be a bit more welcoming, let us add some content to it and reposition the tab bar at the bottom of the page. By default, the tab bar is positioned at the top of the page, but setting the {@link Ext.tab.Panel#tabBarPosition tabBarPosition} config moves it to the page bottom. Then we add some html into the items array to create some content. Items arrays are used to add child items into a container, as shown in the following code:

    @example raw portrait
    Ext.application({
        name: 'Sencha',

        launch: function() {
            Ext.create("Ext.tab.Panel", {
                fullscreen: true,
                tabBarPosition: 'bottom',

                items: [
                    {
                        title: 'Home',
                        iconCls: 'home',
                        html: [
                            '<img src="http://staging.sencha.com/img/sencha.png" />',
                            '<h1>Welcome to Sencha Touch</h1>',
                            "<p>You're creating the Getting Started app. This demonstrates how ",
                            "to use tabs, lists and forms to create a simple app</p>",
                            '<h2>Sencha Touch 2</h2>'
                        ].join("")
                    }
                ]
            });
        }
    });

You can click the Preview button next to the example to inspect the result: you should see some HTML content, but it will not look very good. We add a {@link Ext.Component#cls cls} config to the panel, adding a CSS class that we can target to make things look better. All of the CSS we are adding is in the examples/getting_started/index.html file in the SDK download. Here is how the home page looks at this point:

    @example raw preview portrait
    Ext.application({
        name: 'Sencha',

        launch: function() {
            Ext.create("Ext.tab.Panel", {
                fullscreen: true,
                tabBarPosition: 'bottom',

                items: [
                    {
                        title: 'Home',
                        iconCls: 'home',
                        cls: 'home',

                        html: [
                            '<img src="http://staging.sencha.com/img/sencha.png" />',
                            '<h1>Welcome to Sencha Touch</h1>',
                            "<p>You're creating the Getting Started app. This demonstrates how ",
                            "to use tabs, lists and forms to create a simple app</p>",
                            '<h2>Sencha Touch 2</h2>'
                        ].join("")
                    }
                ]
            });
        }
    });

## Adding the Blogs Page

Now that we have a decent looking home page, we move on to the next screen. In order to keep the code for each page easy to follow, we create one tab at a time and then combine them all together at the end.

For now, we remove the first tab and replace it with a List. We will use Google's <a href="https://developers.google.com/feed/v1/reference">Feed API service</a> to fetch the feeds. Since there is more code involved this time, first we take a look at the result, then we explain how this is accomplished:

    @example raw portrait preview
    Ext.application({
        name: 'Sencha',

        launch: function() {
            Ext.create("Ext.tab.Panel", {
                fullscreen: true,
                tabBarPosition: 'bottom',

                items: [
                    {
                        xtype: 'nestedlist',
                        title: 'Blog',
                        iconCls: 'star',
                        displayField: 'title',

                        store: {
                            type: 'tree',

                            fields: [
                                'title', 'link', 'author', 'contentSnippet', 'content',
                                {name: 'leaf', defaultValue: true}
                            ],

                            root: {
                                leaf: false
                            },

                            proxy: {
                                type: 'jsonp',
                                url: 'https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.feedburner.com/SenchaBlog',
                                reader: {
                                    type: 'json',
                                    rootProperty: 'responseData.feed.entries'
                                }
                            }
                        },

                        detailCard: {
                            xtype: 'panel',
                            scrollable: true,
                            styleHtmlContent: true
                        },

                        listeners: {
                            itemtap: function(nestedList, list, index, element, post) {
                                this.getDetailCard().setHtml(post.get('content'));
                            }
                        }
                    }
                ]
            });
        }
    });

You can click the 'Code Editor' button above the example code to see the full code, but we will go over it piece by piece. At this point, instead of a panel we use a {@link Ext.dataview.NestedList nestedlist}, and populate the list with the most recent blog posts fetched from sencha.com/blog. We use a Nested List component so that we can drill down into the blog entry itself by tapping on the list.

Let us break down the previous code, starting with the list itself:

    @example raw portrait
    Ext.application({
        name: 'Sencha',

        launch: function() {
            Ext.create("Ext.tab.Panel", {
                fullscreen: true,
                tabBarPosition: 'bottom',

                items: [
                    {
                        xtype: 'nestedlist',
                        title: 'Blog',
                        iconCls: 'star',
                        displayField: 'title',

                        store: {
                            type: 'tree',

                            fields: [
                                'title', 'link', 'author', 'contentSnippet', 'content',
                                {name: 'leaf', defaultValue: true}
                            ],

                            root: {
                                leaf: false
                            },

                            proxy: {
                                type: 'jsonp',
                                url: 'https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.feedburner.com/SenchaBlog',
                                reader: {
                                    type: 'json',
                                    rootProperty: 'responseData.feed.entries'
                                }
                            }
                        }
                    }
                ]
            });
        }
    });

In the previous code, we gave the Nested List a number of one-line configurations - title, iconCls, and displayField - and a more detailed one called {@link Ext.dataview.NestedList#store store}. The Store config tells the nested list how to fetch its data. Let us go over each of the following Store config in turn:

* *type: tree* creates a {@link Ext.data.TreeStore tree store}, which NestedList uses.
* *fields* tells the Store what fields we expect in the blog data (title, content, author, and so on).
* *proxy* tells the Store where to fetch its data from. We will examine this more closely in a subsequent section.
* *root* tells the root node it is not a leaf. Since earlier in the code we had set the leaf defaultValue to true,  we need to override that for the root.

Of all the Store configurations, proxy is doing the most important work. We are telling the proxy to use Google's <a href="https://developers.google.com/feed/v1/reference">Feed API service</a> to return our blog data in JSON-P format. This allows us to grab feed data from any blog and view it in our app (for example try swapping the Sencha blog url for http://rss.slashdot.org/Slashdot/slashdot above to see that it fetches Slashdot's feed).

The last part of the proxy definition is a Reader. The reader is the entity that decodes the response from Google into useful data. When Google sends back the blog data, they nest it inside a JSON object that looks a bit like in the following example:

    {
        responseData: {
            feed: {
                entries: [
                    {author: 'Bob', title: 'Great Post', content: 'Really good content...'}
                ]
            }
        }
    }

In this code, the important part is the entries array, so we set our Reader's {@link Ext.data.reader.Json#rootProperty rootProperty} to 'responseData.feed.entries' and let the framework do the rest.

### Digging In

Now that we have our nested list fetching and showing data, the last thing we need to do is to allow users to tap on an entry to read it. In order to add this functionality, we add two more configurations to our Nested List, as follows:

    {
        xtype: 'nestedlist',
        //all other configurations as above
        
        detailCard: {
            xtype: 'panel',
            scrollable: true,
            styleHtmlContent: true
        },

        listeners: {
            itemtap: function(nestedList, list, index, element, post) {
                this.getDetailCard().setHtml(post.get('content'));
            }
        }
    }

In this code sample we set up a {@link Ext.dataview.NestedList#detailCard detailCard}, which is a useful feature of Nested List that allows you to show a different view when a user taps on an item. We configured our detailCard to be a scrollable {@link Ext.Panel Panel} that uses {@link Ext.Panel#styleHtmlContent styleHtmlContent} to make the text look good.

The final step is adding an {@link Ext.dataview.NestedList#itemtap itemtap} listener, which calls our function whenever an item is tapped on. The function sets the detailCard's HTML to the content of the post you tapped on and the framework takes care of the rest, animating the detail card into view to make the post appear. This was the only line of code we had to write in order to make the blog reader work.

## Creating a Contact Form

The final thing we do for our app is create a contact form. We take the user's name, email address, and a message, and use a {@link Ext.form.FieldSet FieldSet} to make it look good. The code for this functionality is as follows:

    @example raw portrait
    Ext.application({
        name: 'Sencha',

        launch: function() {
            Ext.create("Ext.tab.Panel", {
                fullscreen: true,
                tabBarPosition: 'bottom',

                items: [
                    {
                        title: 'Contact',
                        iconCls: 'user',
                        xtype: 'formpanel',
                        url: 'contact.php',
                        layout: 'vbox',

                        items: [
                            {
                                xtype: 'fieldset',
                                title: 'Contact Us',
                                instructions: '(email address is optional)',
                                items: [
                                    {
                                        xtype: 'textfield',
                                        label: 'Name'
                                    },
                                    {
                                        xtype: 'emailfield',
                                        label: 'Email'
                                    },
                                    {
                                        xtype: 'textareafield',
                                        label: 'Message'
                                    }
                                ]
                            },
                            {
                                xtype: 'button',
                                text: 'Send',
                                ui: 'confirm',
                                handler: function() {
                                    this.up('formpanel').submit();
                                }
                            }
                        ]
                    }
                ]
            });
        }
    });

This time we create a {@link Ext.form.Panel form} that contains a {@link Ext.form.FieldSet fieldset}. The fieldset contains three fields - one for name, one for email, and one for a message. We use a {@link Ext.layout.VBox VBox layout}, which arranges the items vertically on the page, one above the other.

At the bottom of the panel we added a {@link Ext.Button Button} with a tap {@link Ext.Button#handler handler}. The handler uses the {@link Ext.Container#up up} method, which returns the form panel containing the button. We then call {@link Ext.form.Panel#method-submit submit} to submit the form, which sends it to the specified URL ('contact.php').

## Putting It All Together

After having created each view individually, lets bring them all together into a complete app: 

    @example raw preview portrait
    //We've added a third and final item to our tab panel - scroll down to see it
    Ext.application({
        name: 'Sencha',

        launch: function() {
            Ext.create("Ext.tab.Panel", {
                fullscreen: true,
                tabBarPosition: 'bottom',

                items: [
                    {
                        title: 'Home',
                        iconCls: 'home',
                        cls: 'home',
                        html: [
                            '<img width="65%" src="http://staging.sencha.com/img/sencha.png" />',
                            '<h1>Welcome to Sencha Touch</h1>',
                            "<p>You're creating the Getting Started app. This demonstrates how ",
                            "to use tabs, lists and forms to create a simple app</p>",
                            '<h2>Sencha Touch 2</h2>'
                        ].join("")
                    },
                    {
                        xtype: 'nestedlist',
                        title: 'Blog',
                        iconCls: 'star',
                        displayField: 'title',

                        store: {
                            type: 'tree',

                            fields: [
                                'title', 'link', 'author', 'contentSnippet', 'content',
                                {name: 'leaf', defaultValue: true}
                            ],

                            root: {
                                leaf: false
                            },

                            proxy: {
                                type: 'jsonp',
                                url: 'https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.feedburner.com/SenchaBlog',
                                reader: {
                                    type: 'json',
                                    rootProperty: 'responseData.feed.entries'
                                }
                            }
                        },

                        detailCard: {
                            xtype: 'panel',
                            scrollable: true,
                            styleHtmlContent: true
                        },

                        listeners: {
                            itemtap: function(nestedList, list, index, element, post) {
                                this.getDetailCard().setHtml(post.get('content'));
                            }
                        }
                    },
                    //this is the new item
                    {
                        title: 'Contact',
                        iconCls: 'user',
                        xtype: 'formpanel',
                        url: 'contact.php',
                        layout: 'vbox',

                        items: [
                            {
                                xtype: 'fieldset',
                                title: 'Contact Us',
                                instructions: '(email address is optional)',
                                items: [
                                    {
                                        xtype: 'textfield',
                                        label: 'Name'
                                    },
                                    {
                                        xtype: 'emailfield',
                                        label: 'Email'
                                    },
                                    {
                                        xtype: 'textareafield',
                                        label: 'Message'
                                    }
                                ]
                            },
                            {
                                xtype: 'button',
                                text: 'Send',
                                ui: 'confirm',
                                handler: function() {
                                    this.up('formpanel').submit();
                                }
                            }
                        ]
                    }
                ]
            });
        }
    });

You can find the full source code of the Getting Started app in the examples/getting_started folder of the Sencha Touch 2.1 SDK.