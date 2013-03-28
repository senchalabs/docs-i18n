# Using Carousels in Sencha Touch

{@link Ext.carousel.Carousel Carousels}, like [tabs](#!/guide/tabs), are a great way to allow users to swipe through multiple full screen pages.
A Carousel shows only one of its pages at a time, but allows you to browse through other pages using a swipe gesture. You can think of a Carousel as a single active item, with the rest of the items stretching away left and right. Indicator dots indicate how many available screens are available to swipe through, as shown in the following image:

{@img carousel.jpg}

Carousels can be oriented either horizontally or vertically and are easy to configure - they just work like any other Container component. The following code sample shows how to set up a simple horizontal Carousel:

    @example preview
    Ext.create('Ext.Carousel', {
        fullscreen: true,

        defaults: {
            styleHtmlContent: true
        },

        items: [
            {
                html : 'Item 1',
                style: 'background-color: #5E99CC'
            },
            {
                html : 'Item 2',
                style: 'background-color: #759E60'
            },
            {
                html : 'Item 3'
            }
        ]
    });

Swiping your finger left and right over the carousel swaps between the two items defined above. It also updates the indicator icon to let you know which page you are currently on. We can also make Carousels orient themselves vertically, as shown in the following code sample:

    @example preview
    Ext.create('Ext.Carousel', {
        fullscreen: true,
        direction: 'vertical',

        defaults: {
            styleHtmlContent: true
        },

        items: [
            {
                html : 'Item 1',
                style: 'background-color: #759E60'
            },
            {
                html : 'Item 2',
                style: 'background-color: #5E99CC'
            }
        ]
    });

You can place any component into a Carousel, for example the following code places a list and a form into a horizontal carousel:

    @example portrait preview
    Ext.create('Ext.Carousel', {
        fullscreen: true,

        items: [
            {
                xtype: 'list',

                items: {
                    xtype: 'toolbar',
                    docked: 'top',
                    title: 'Sencha Touch Team'
                },

                store: {
                    fields: ['name'],
                    data: [
                        {name: 'Rob'},
                        {name: 'Ed'},
                        {name: 'Jacky'},
                        {name: 'Jamie'},
                        {name: 'Tommy'},
                        {name: 'Abe'}
                    ]
                },

                itemTpl: '{name}'
            },
            {
                xtype: 'fieldset',
                items: [
                    {
                        xtype: 'toolbar',
                        docked: 'top',
                        title: 'Login'
                    },
                    {
                        xtype: 'textfield',
                        label: 'Name'
                    },
                    {
                        xtype: 'passwordfield',
                        label: 'Password'
                    }
                ]
            }
        ]
    });
