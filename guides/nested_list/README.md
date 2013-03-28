# Using Nested List in Sencha Touch

{@link Ext.NestedList Nested List} is a component in Sencha Touch which provides a miller column interface for navigating between nested sets of data with a clean and easy to use interface.

    @example miniphone preview
    Ext.define('ListItem', {
        extend: 'Ext.data.Model',
        config: {
            fields: ['text']
        }
    });

    var treeStore = Ext.create('Ext.data.TreeStore', {
        model: 'ListItem',
        defaultRootProperty: 'items',
        root: {
            items: [
                {
                    text: 'Drinks',
                    items: [
                        {
                            text: 'Water',
                            items: [
                                { text: 'Still', leaf: true },
                                { text: 'Sparkling', leaf: true }
                            ]
                        },
                        { text: 'Soda', leaf: true }
                    ]
                },
                {
                    text: 'Snacks',
                    items: [
                        { text: 'Nuts', leaf: true },
                        { text: 'Pretzels', leaf: true },
                        { text: 'Wasabi Peas', leaf: true  }
                    ]
                }
            ]
        }
    });

    Ext.create('Ext.NestedList', {
        fullscreen: true,
        store: treeStore
    });

## Creating a Simple Nested List

Creating a simple {@link Ext.NestedList Nested List} is done using the following configurations:

* {@link Ext.NestedList#store store} - this is the data store where the {@link Ext.NestedList Nested List} gets its data from. {@link Ext.NestedList Nested List} uses the {@link Ext.data.TreeStore} class as {@link Ext.NestedList Nested List} which has a tree-like UI.
* {@link Ext.NestedList#displayField displayField} - this is the field of the {@link Ext.NestedList#store store} which is displayed on each list item in the {@link Ext.NestedList Nested List}. This configuration defaults to `text`, so it is not needed in some cases.

Let us look at the following code needed to create a basic {@link Ext.NestedList nested list}:

    @example miniphone
    Ext.define('ListItem', {
        extend: 'Ext.data.Model',
        config: {
            fields: ['text']
        }
    });

    var treeStore = Ext.create('Ext.data.TreeStore', {
        model: 'ListItem',
        defaultRootProperty: 'items',
        root: {
            items: [
                {
                    text: 'Drinks',
                    items: [
                        {
                            text: 'Water',
                            items: [
                                { text: 'Still', leaf: true },
                                { text: 'Sparkling', leaf: true }
                            ]
                        },
                        { text: 'Soda', leaf: true }
                    ]
                },
                {
                    text: 'Snacks',
                    items: [
                        { text: 'Nuts', leaf: true },
                        { text: 'Pretzels', leaf: true },
                        { text: 'Wasabi Peas', leaf: true  }
                    ]
                }
            ]
        }
    });

    Ext.create('Ext.NestedList', {
        fullscreen: true,
        store: treeStore
    });

**Let us walk through the previous code:**

* First we define our `ListItem` model. This is a simple {@link Ext.data.Model model} with one field defined which is our `text` field. This is the only information needed at this point, and it will be displayed in the list for each item.
* Next we create our `treeStore`.
* The first property we pass it is the prevously defined {@link Ext.data.Model model} instance.
* Then we define the {@link Ext.data.TreeStore#defaultRootProperty defaultRootProperty}` of the data that will be passed into our treeStore. In our case, this will be `items`.
* Then we define the {@link Ext.data.TreeStore#root root} property. This is the data that will be passed into the treeStore.

### Tree Store Data

The most confusing part of the previous example is definitely the {@link Ext.data.TreeStore TreeStore} and its {@link Ext.data.TreeStore#root root/data}. Let us look at the code again:

    var treeStore = Ext.create('Ext.data.TreeStore', {
        model: 'ListItem',
        defaultRootProperty: 'items',
        root: {
            items: [
                {
                    text: 'Drinks',
                    items: [
                        {
                            text: 'Water',
                            items: [
                                { text: 'Still', leaf: true },
                                { text: 'Sparkling', leaf: true }
                            ]
                        },
                        { text: 'Soda', leaf: true }
                    ]
                },
                {
                    text: 'Snacks',
                    items: [
                        { text: 'Nuts', leaf: true },
                        { text: 'Pretzels', leaf: true },
                        { text: 'Wasabi Peas', leaf: true  }
                    ]
                }
            ]
        }
    });

The first two lines are easy to understand: first we create an instance of a {@link Ext.data.TreeStore TreeStore} and then we give it a model which we defined above.

{@link Ext.data.TreeStore#defaultRootProperty defaultRootProperty} is where it starts to get a little bit trickier. It is the root field of your data which tells the {@link Ext.NestedList nested list} where the data starts. This is the same, regardless whether it is inline JavaScript (like above) or remote data (which we will come to later). This properly also applies to each item in your data, even if it is nested.

The {@link Ext.data.TreeStore#root root} property in this case is the data for this {@link Ext.data.TreeStore store}. It is an object, and it only has one property - `items` - which is also our `defaultRootProperty`. Within each item, we specify a `text` property which we also defined in our `ListItem` model above. Note that the **Drinks**, **Water**, and **Snacks** items also have children (using the `defaultRootProperty` `items`).

Some items also have a `leaf` property. This means that when a user taps on this item, a {@link Ext.NestedList#detailCard detail card} appears if it configured - but we leave that until later.

## Loading Remote Data

In the previous example we added some inline data, but when creating real world examples this is very rarely the case. What if we want to load data from a remote JSON file?

It is actually very similar. Let us look at the following JSON file that we want to load:

    {
        "items": [
            {
                "text": "Drinks",
                "items": [
                    {
                        "text": "Water",
                        "items": [
                            { "text": "Still", "leaf": true },
                            { "text": "Sparkling", "leaf": true }
                        ]
                    },
                    { "text": "Soda", "leaf": true }
                ]
            },
            {
                "text": "Snacks",
                "items": [
                    { "text": "Nuts", "leaf": true },
                    { "text": "Pretzels", "leaf": true },
                    { "text": "Wasabi Peas", "leaf": true  }
                ]
            }
        ]
    }

Since it is identical to the inline {@link Ext.data.TreeStore#root root} property we defined previously, let us look at the differences when creating the {@link Ext.data.TreeStore TreeStore}:

    var treeStore = Ext.create('Ext.data.TreeStore', {
        model: 'ListItem',
        defaultRootProperty: 'items',
        proxy: {
            type: 'ajax',
            url: 'data.json'
        }
    });

We create the store using {@link Ext.ClassManager#create Ext.create} and set the `model` and `defaultRootProperty` properties just like before, but then we set a `proxy`. This property tells the {@link Ext.data.TreeStore TreeStore} to load its data using a specify proxy. In this case we give it a `type` of 'ajax' (because it is loading a remote file) and then the `url` of the JSON file.

## Detail Cards

A {@link Ext.NestedList#detailCard detail card} is shown (if configured) when a user taps on an item that is a `leaf` (defined in the data the {@link Ext.data.TreeStore TreeStore} loads in the previous example). It can be any type of {@link Ext.Component Component}, which means it is very customizable. You can set the detail card using the {@link Ext.NestedList#detailCard detailCard} config in the nested list configuration. Let us look at the following simple example:

    @example miniphone preview
    Ext.define('ListItem', {
        extend: 'Ext.data.Model',
        config: {
            fields: ['text']
        }
    });

    var treeStore = Ext.create('Ext.data.TreeStore', {
        model: 'ListItem',
        defaultRootProperty: 'items',
        root: {
            items: [
                {
                    text: 'Drinks',
                    items: [
                        {
                            text: 'Water',
                            items: [
                                { text: 'Still', leaf: true },
                                { text: 'Sparkling', leaf: true }
                            ]
                        },
                        { text: 'Soda', leaf: true }
                    ]
                },
                {
                    text: 'Snacks',
                    items: [
                        { text: 'Nuts', leaf: true },
                        { text: 'Pretzels', leaf: true },
                        { text: 'Wasabi Peas', leaf: true  }
                    ]
                }
            ]
        }
    });

    Ext.create('Ext.NestedList', {
        fullscreen: true,
        store: treeStore,
        detailCard: {
            html: 'You are viewing the detail card!'
        }
    });

As you can see, when you tap on one of the `leaf` items, the {@link Ext.NestedList#detailCard detail card} becomes visible. So let us look at the following code:

    Ext.create('Ext.NestedList', {
        fullscreen: true,
        store: treeStore,
        detailCard: {
            html: 'You are viewing the detail card!'
        }
    });

In this example we set the {@link Ext.NestedList#detailCard detailCard} to be an object, with a config of {@link Ext.Component#html html} set. This object is automatically transformed into a {@link Ext.Component Component} (just like when you call {@link Ext.Container#method-add add} on a {@link Ext.Container Container}).

What if we wanted to set the data in the {@link Ext.NestedList#detailCard detailCard} depending on what item we tap? Let us look at how we do that:

    Ext.create('Ext.NestedList', {
        fullscreen: true,
        store: treeStore,
        detailCard: {
            html: 'You are viewing the detail card!'
        },
        listeners: {
            leafitemtap: function(nestedList, list, index, target, record) {
                var detailCard = nestedList.getDetailCard();
                detailCard.setHtml('You selected: ' + record.get('text'));
            }
        }
    });

Note how we added the {@link Ext.NestedList#listeners listeners} config. We do this because we want to listen to the **{@link Ext.NestedList#event-leafitemtap leafitemtap}** event, which is called when any leaf item is tapped on.

When that event is fired, it is passed a number of arguments:

* `nestedList` - this is a reference to the {@link Ext.NestedList nested list} instance.
* `list` - this is a reference to the last {@link Ext.dataview.List list}, where the item with the `leaf` config was tapped.
* `index` - the index of the {@link Ext.data.Model record} in the {@link Ext.dataview.List list} {@link Ext.dataview.List#store store}.
* `target` - the {@link Ext.dom.Element element} which was tapped on.
* `record` - the {@link Ext.data.Model record} of the item which was tapped on.

Since we know when someone taps a `leaf` item, and we know which `record` was tapped, we can update the {@link Ext.NestedList#detailCard detail card} with some data.

To get the {@link Ext.NestedList#detailCard detailCard}, we can use the {@link Ext.NestedList#getDetailCard} method, and then with that we can update the {@link Ext.Component#html html} config of our {@link Ext.NestedList#detailCard detailCard}.

Eventually, let us look at this complete example:

    @example miniphone preview
    Ext.define('ListItem', {
        extend: 'Ext.data.Model',
        config: {
            fields: ['text']
        }
    });

    var treeStore = Ext.create('Ext.data.TreeStore', {
        model: 'ListItem',
        defaultRootProperty: 'items',
        root: {
            items: [
                {
                    text: 'Drinks',
                    items: [
                        {
                            text: 'Water',
                            items: [
                                { text: 'Still', leaf: true },
                                { text: 'Sparkling', leaf: true }
                            ]
                        },
                        { text: 'Soda', leaf: true }
                    ]
                },
                {
                    text: 'Snacks',
                    items: [
                        { text: 'Nuts', leaf: true },
                        { text: 'Pretzels', leaf: true },
                        { text: 'Wasabi Peas', leaf: true  }
                    ]
                }
            ]
        }
    });

    Ext.create('Ext.NestedList', {
        fullscreen: true,
        store: treeStore,
        detailCard: {
            html: 'You are viewing the detail card!'
        },
        listeners: {
            leafitemtap: function(nestedList, list, index, target, record) {
                var detailCard = nestedList.getDetailCard();
                detailCard.setHtml('You selected: ' + record.get('text'));
            }
        }
    });

## Detail Container

Nested List has a {@link Ext.NestedList#detailContainer detailContainer} configuration which is used to specify the container of the {@link Ext.NestedList#detailCard detailCard}. This is used when you want the {@link Ext.NestedList#detailCard detailCard} to be in another container, which is often the case when the screen size is bigger than that of a normal phone.

Let us look at an example of this in action:

    @example preview
    Ext.define('ListItem', {
        extend: 'Ext.data.Model',
        config: {
            fields: ['text']
        }
    });

    var treeStore = Ext.create('Ext.data.TreeStore', {
        model: 'ListItem',
        defaultRootProperty: 'items',
        root: {
            items: [
                {
                    text: 'Drinks',
                    items: [
                        {
                            text: 'Water',
                            items: [
                                { text: 'Still', leaf: true },
                                { text: 'Sparkling', leaf: true }
                            ]
                        },
                        { text: 'Soda', leaf: true }
                    ]
                },
                {
                    text: 'Snacks',
                    items: [
                        { text: 'Nuts', leaf: true },
                        { text: 'Pretzels', leaf: true },
                        { text: 'Wasabi Peas', leaf: true  }
                    ]
                }
            ]
        }
    });

    var detailContainer = Ext.create('Ext.Container', {
        layout: 'card',
        flex: 1
    });

    var nestedList = Ext.create('Ext.NestedList', {
        store: treeStore,
        detailContainer: detailContainer,
        detailCard: true,
        listeners: {
            leafitemtap: function(nestedList, list, index, target, record) {
                var detailCard = nestedList.getDetailCard();
                detailCard.setHtml('You selected: ' + record.get('text'));
            }
        },
        flex: 1
    });

    Ext.Viewport.add({
        layout: 'hbox',
        items: [
            nestedList,
            detailContainer
        ]
    });

As you can see, we now have the following two items in the Viewport:

- The {@link Ext.NestedList nested list}.
- A new container which we specify as the {@link Ext.NestedList#detailContainer detailContainer}.

To set the configuration you give it a reference to the container that is to be used as the {@link Ext.NestedList#detailContainer detail container}:

    var detailContainer = Ext.create('Ext.Container', {
        layout: 'card'
    });

    var nestedList = Ext.create('Ext.NestedList', {
        store: treeStore,
        detailCard: true,
        detailContainer: detailContainer
    });

If you do not specify a {@link Ext.NestedList#detailContainer detailContainer}, the {@link Ext.NestedList#detailCard detailCard} is added into the {@link Ext.NestedList nested list}.
