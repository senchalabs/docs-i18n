# Using DataViews in Sencha Touch

{@link Ext.dataview.DataView DataView} makes it easy to create dynamically a number of components, usually based off a {@link Ext.data.Store Store}. It is used for rendering data from your server backend or any other data source, and it powers components such as {@link Ext.List}.

You can use {@link Ext.dataview.DataView DataView} whenever you want to show repeatedly sets of the same component, for example in cases such as the following:

* A list of messages in an email app
* A list of the latest news or tweets
* A tiled set of albums in an HTML5 music player

## Creating a Simple DataView

At its simplest, a {@link Ext.dataview.DataView DataView} is a {@link Ext.data.Store Store} full of data and a template that is used to render each store item, as illustrated by the following example:

    @example
    var touchTeam = Ext.create('Ext.DataView', {
        fullscreen: true,

        store: {
            fields: ['name', 'age'],
            data: [
                {name: 'Jamie Avins',  age: 100},
                {name: 'Rob Dougan',   age: 21},
                {name: 'Tommy Maintz', age: 24},
                {name: 'Jacky Nguyen', age: 24},
                {name: 'Ed Spencer',   age: 26}
            ]
        },

        itemTpl: '{name} is {age} years old'
    });

In this example we defined the store inline, so all data is local and therefore does not have to be loaded from a server. For each of the five data items defined in the {@link Ext.data.Store Store}, {@link Ext.dataview.DataView DataView} renders a {@link Ext.Component Component} and passes in the name and age data. The component uses the specified {@link Ext.dataview.DataView#itemTpl tpl} and renders the data inside the curly bracket placeholders.

Because {@link Ext.dataview.DataView DataView} is integrated with {@link Ext.data.Store Store}, any changes to the {@link Ext.data.Store Store} are immediately reflected on the screen. For example, if we added a new record to the {@link Ext.data.Store Store}, it would be rendered into our {@link Ext.dataview.DataView DataView}:

    touchTeam.getStore().add({
        name: 'Abe Elias',
        age: 33
    });

After adding the new record as shown in the previous code sample, we do not have to manually update the {@link Ext.dataview.DataView DataView}, since it is automatically updated. The same would happen if we modified one of the existing records in the {@link Ext.data.Store Store}:

    touchTeam.getStore().getAt(0).set('age', 42);

This will get the first record in the {@link Ext.data.Store Store} (Jamie), change his age to 42 and automatically update the screen content.

## Loading Data from a Server

We often want to load data from our server or some other web service, so that we do not have to hard-code the data content locally. Let us say we wanted to load the latest tweets about Sencha Touch into a {@link Ext.dataview.DataView DataView}, and for each tweet render the user's profile picture, the user name and the tweet message. To do this we need to modify the {@link Ext.dataview.DataView#store store} and {@link Ext.dataview.DataView#itemTpl itemTpl} as follows:

    @example
    Ext.create('Ext.DataView', {
        fullscreen: true,

        store: {
            autoLoad: true,
            fields: ['from_user', 'text', 'profile_image_url'],

            proxy: {
                type: 'jsonp',
                url: 'http://search.twitter.com/search.json?q=Sencha Touch',

                reader: {
                    type: 'json',
                    rootProperty: 'results'
                }
            }
        },

        itemTpl: '<img src="{profile_image_url}" /><h2>{from_user}</h2><p>{text}</p>'
    });

In this example, the {@link Ext.dataview.DataView DataView} no longer contains data items, instead we have provided a {@link Ext.data.proxy.Proxy Proxy}, which fetches the data. In this case we used a JSON-P proxy that loads data using Twitter's JSON-P search API. We also specified the fields present for each tweet, and used Store's {@link Ext.data.Store#autoLoad autoLoad} configuration to load the data automatically. Finally, we configured a Reader to decode the response from Twitter, indicating it to expect JSON and that the tweets are found in the 'results' part of the JSON response.

As the last operation we updated the template to render the image, the twitter username and the message. Finally, all that is still required is adding some CSS to style the list as desired.

## Styling a DataView

You may have realized by now that although our {@link Ext.dataview.DataView DataView} is displaying data from our {@link Ext.data.Store Store}, it does not have any default styling. The lack of default styling is by design, but adding custom CSS is very simple. {@link Ext.dataview.DataView DataView} has two configurations that support targeting your custom CSS to the view: {@link Ext.dataview.DataView#baseCls baseCls} and {@link Ext.dataview.DataView#itemCls itemCls}. {@link Ext.dataview.DataView#baseCls baseCls} is used to add a `className` around the outer element of the {@link Ext.dataview.DataView DataView}. The {@link Ext.dataview.DataView#itemCls itemCls} you provide is added onto each item that is rendered into our {@link Ext.dataview.DataView DataView}.

If you do *not* specify an {@link Ext.dataview.DataView#itemCls itemCls}, it automatically takes the {@link Ext.dataview.DataView#baseCls baseCls} configuration (which defaults to `x-dataview`) and prepends `-item` to it. As a result, each item will have a className of `x-dataview-item`.

Before adding the configuration, we need to create the custom CSS shown in the following example:

    .my-dataview-item {
        background: #ddd;
        padding: 1em;
        border-bottom: 1px solid #ccc;
    }
    .my-dataview-item img {
        float: left;
        margin-right: 1em;
    }
    .my-dataview-item h2 {
        font-weight: bold;
    }

Once the CSS is complete, we can go back to our previous Twitter example and add the {@link Ext.dataview.DataView#baseCls baseCls} configuration:

    @example
    Ext.create('Ext.DataView', {
        fullscreen: true,

        store: {
            autoLoad: true,
            fields: ['from_user', 'text', 'profile_image_url'],

            proxy: {
                type: 'jsonp',
                url: 'http://search.twitter.com/search.json?q=Sencha Touch',

                reader: {
                    type: 'json',
                    root: 'results'
                }
            }
        },

        itemTpl: '<img src="{profile_image_url}" /><h2>{from_user}</h2><p>{text}</p>',

        baseCls: 'my-dataview'

        //As described above, we don't need to set itemCls in most cases as it will already add a className
        //generated from the baseCls to each item.
        //itemCls: 'my-dataview-item'
    });

## Component DataView

In the previous example we created a {@link Ext.dataview.DataView DataView} with an {@link Ext.dataview.DataView#itemTpl itemTpl}, which means each item is rendered from a {@link Ext.XTemplate template}. However, sometimes you need each item to be a component, so you can provide a rich UI for users. In Sencha Touch, we introduced the {@link Ext.dataview.DataView#useComponents useComponents} configuration which allows you to address this scenario.

Creating a component {@link Ext.dataview.DataView DataView} is very similar to creating a normal template-based {@link Ext.dataview.DataView DataView} as previously shown, however you must define the item view used when rendering each item in your list.

    Ext.define('MyListItem', {
        extend: 'Ext.dataview.component.DataItem',
        requires: ['Ext.Button'],
        xtype: 'mylistitem',

        config: {
            nameButton: true,

            dataMap: {
                getNameButton: {
                    setText: 'name'
                }
            }
        },

        applyNameButton: function(config) {
            return Ext.factory(config, Ext.Button, this.getNameButton());
        },

        updateNameButton: function(newNameButton, oldNameButton) {
            if (oldNameButton) {
                this.remove(oldNameButton);
            }

            if (newNameButton) {
                this.add(newNameButton);
            }
        }
    });

This example shows how to define a component-based {@link Ext.dataview.DataView DataView} item component. There are a few important notes about this example:

* We must extend {@link Ext.dataview.component.DataItem} for each item. This is an abstract class which handles the record handling for each item.
* Following the extend code we require {@link Ext.Button}, because we intend to insert a {@link Ext.Button button} inside the item component.
* We specify the `xtype` for this item component.
* Inside the config block we define the `nameButton` item. This custom configuration added to the component will be transformed into a {@link Ext.Button button} by the [class system](#!/guide/class_system). By default we set it to `true` , but this could also be a configuration block. This configuration automatically generates getters and setters for the `nameButton` item.
* We define the {@link Ext.dataview.component.DataItem#dataMap dataMap}. The dataMap is a map between the data of a {@link Ext.data.Model record} and this view. The `getNameButton` is the getter for the instance you want to update; so in this case we want to get the `nameButton` configuration of this component. Inside that block we then give it the setter for that instance, `setText` in this case and give it the field of the record we are passing. As a result, once this item component gets a record, it gets the `nameButton` and then calls `setText` with the `name` value of the record.
* Finally we define the apply method for the `nameButton` item. The apply method uses {@link Ext#factory} to transform the configuration passed into an instance of {@link Ext.Button}. That instance is then returned, which  then causes `updateNameButton` to be called. The `updateNameButton` method removes the old nameButton instance if it exists, and adds the new nameButton instance if it exists.

After having created the item component, we can create the component {@link Ext.dataview.DataView DataView}, similar to previous examples.

    Ext.create('Ext.DataView', {
        fullscreen: true,

        store: {
            fields: ['name', 'age'],
            data: [
                {name: 'Jamie Avins',  age: 100},
                {name: 'Rob Dougan',   age: 21},
                {name: 'Tommy Maintz', age: 24},
                {name: 'Jacky Nguyen', age: 24},
                {name: 'Ed Spencer',   age: 26}
            ]
        },

        useComponents: true,
        defaultType: 'mylistitem'
    });

This code has two key additions. First, we added the {@link Ext.dataview.DataView#useComponents useComponents} configuration and set it to `true`. Second, we set the {@link Ext.dataview.DataView#defaultType defaultType} configuration to the previously created `mylistitem` item component. This tells the {@link Ext.dataview.DataView DataView} to use the defined item component as the view for each item.

If we run this code together, we can see the component {@link Ext.dataview.DataView DataView} in action.

    @example preview
    Ext.define('MyListItem', {
        extend: 'Ext.dataview.component.DataItem',
        requires: ['Ext.Button'],
        xtype: 'mylistitem',

        config: {
            nameButton: true,

            dataMap: {
                getNameButton: {
                    setText: 'name'
                }
            }
        },

        applyNameButton: function(config) {
            return Ext.factory(config, Ext.Button, this.getNameButton());
        },

        updateNameButton: function(newNameButton, oldNameButton) {
            if (oldNameButton) {
                this.remove(oldNameButton);
            }

            if (newNameButton) {
                this.add(newNameButton);
            }
        }
    });

    Ext.create('Ext.DataView', {
        fullscreen: true,

        store: {
            fields: ['name', 'age'],
            data: [
                {name: 'Jamie Avins',  age: 100},
                {name: 'Rob Dougan',   age: 21},
                {name: 'Tommy Maintz', age: 24},
                {name: 'Jacky Nguyen', age: 24},
                {name: 'Ed Spencer',   age: 26}
            ]
        },

        useComponents: true,
        defaultType: 'mylistitem'
    });

The great benefit of this approach is the flexibility it adds to your dataviews. Since each item component has access to its own {@link Ext.dataview.component.DataItem#record record}, you can do just about anything with it.

In the following example we add to the `nameButton` an event listener to the tap event, which alerts the user with the age of the selected person.

    Ext.define('MyListItem', {
        //...

        updateNameButton: function(newNameButton, oldNameButton) {
            if (oldNameButton) {
                this.remove(oldNameButton);
            }

            if (newNameButton) {
                // add an event listeners for the `tap` event onto the new button, and tell it to call the onNameButtonTap method
                // when it happens
                newNameButton.on('tap', this.onNameButtonTap, this);

                this.add(newNameButton);
            }
        },

        onNameButtonTap: function(button, e) {
            var record = this.getRecord();

            Ext.Msg.alert(
                record.get('name'), // the title of the alert
                "The age of this person is: " + record.get('age') // the message of the alert
            );
        }
    });

Finally, after adding this code to our previous example, we get the following finished result:

    @example preview
    Ext.define('MyListItem', {
        extend: 'Ext.dataview.component.DataItem',
        requires: ['Ext.Button'],
        xtype: 'mylistitem',

        config: {
            nameButton: true,

            dataMap: {
                getNameButton: {
                    setText: 'name'
                }
            }
        },

        applyNameButton: function(config) {
            return Ext.factory(config, Ext.Button, this.getNameButton());
        },

        updateNameButton: function(newNameButton, oldNameButton) {
            if (oldNameButton) {
                this.remove(oldNameButton);
            }

            if (newNameButton) {
                // add an event listeners for the `tap` event onto the new button, and tell it to call the onNameButtonTap method
                // when it happens
                newNameButton.on('tap', this.onNameButtonTap, this);

                this.add(newNameButton);
            }
        },

        onNameButtonTap: function(button, e) {
            var record = this.getRecord();

            Ext.Msg.alert(
                record.get('name'), // the title of the alert
                "The age of this person is: " + record.get('age') // the message of the alert
            );
        }
    });

    Ext.create('Ext.DataView', {
        fullscreen: true,

        store: {
            fields: ['name', 'age'],
            data: [
                {name: 'Jamie Avins',  age: 100},
                {name: 'Rob Dougan',   age: 21},
                {name: 'Tommy Maintz', age: 24},
                {name: 'Jacky Nguyen', age: 24},
                {name: 'Ed Spencer',   age: 26}
            ]
        },

        useComponents: true,
        defaultType: 'mylistitem'
    });
