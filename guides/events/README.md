# Understanding and Using Events in Sencha Touch

The Components and classes of Sencha Touch fire a broad range of events at various points in their lifecycle. Events allow your code to react to changes around it and are a key concept within Sencha Touch.

## What Are Events?

Events are fired whenever something interesting happens to one of your classes. For example, when any Component is rendered to the screen, its {@link Ext.Component#event-painted painted} event is fired. We can listen for that event by configuring a simple {@link Ext.mixin.Observable#listeners listeners} config:

    @example
    Ext.create('Ext.Panel', {
        html: 'My Panel',
        fullscreen: true,

        listeners: {
            painted: function() {
                Ext.Msg.alert('I was painted to the screen');
            }
        }
    });

When you click the "Live Preview" button above, you will see a Panel rendered to the screen, followed by the defined alert message. All events fired by a class are listed in its API page - for example at the time of writing Ext.Panel has 28 events:

<a href="#!/api/Ext.Panel">{@img event-list.png Hover over the Events button to quickly see which events are available}</a>

## Listening to Events

Although the painted event itself is useful in some cases, there are other events that you are much more likely to use. For example, {@link Ext.Button Buttons} fire {@link Ext.Button#event-tap tap} events whenever they are tapped on:

    @example
    Ext.Viewport.add({
        xtype: 'button',
        centered: true,
        text: 'My Button',

        listeners: {
            tap: function() {
                alert("You tapped me");
            }
        }
    });

You can add as many event listeners as you like. In the following example we are going to confound users by calling *this.hide()* inside our tap listener to hide the Button, only to show it again a second later. When *this.hide()* is called, the Button is hidden and the {@link Ext.Button#event-hide hide event} fired. The hide event in turn triggers our hide listener, which waits for a second before it shows the Button again:

    @example
    Ext.Viewport.add({
        xtype: 'button',
        centered: true,
        text: 'My Button',

        listeners: {
            tap: function() {
                this.hide();
            },
            hide: function() {
                //waits 1 second (1000ms) then shows the button again
                Ext.defer(function() {
                    this.show();
                }, 1000, this);
            }
        }
    });

Event listeners are called every time an event is fired, so you can continue hiding and showing the button for all eternity.

## Config-driven Events

Most classes are reconfigurable at run time, meaning that you can change their configurations such as height, width, or content at any time and the Component will correctly update itself on screen. Many of these configuration changes trigger the firing of an event - for example 14 of the Button's component 24 events have names such as {@link Ext.Button#event-widthchange widthchange}, {@link Ext.Button#event-hiddenchange hiddenchange} and {@link Ext.Button#event-centeredchange centeredchange}.

In the following example the tap handler calls this.setWidth() to set a random width on our button. The {@link Ext.Button#event-widthchange widthchange} listener is informed immediately of the change, along with the new and old width values:

    @example
    Ext.Viewport.add({
        xtype: 'button',
        centered: true,
        text: 'Click me',

        listeners: {
            tap: function() {
                var randomWidth = 100 + Math.round(Math.random() * 200);

                this.setWidth(randomWidth);
            },
            widthchange: function(button, newWidth, oldWidth) {
                alert('My width changed from ' + oldWidth + ' to ' + newWidth);
            }
        }
    });

Every event that ends in 'change' is fired as a result of a config option that has changed. Note that although listening to these events is like listening to any other events, it is useful to know the convention.

## Adding Listeners Later

Previous examples involved passing listeners in when the class is instantiated. If we already have an instance though, we can still add listeners later on using the {@link Ext.mixin.Observable#on on} function:

    @example
    var myButton = Ext.Viewport.add({
        xtype: 'button',
        centered: true,
        text: 'Click me'
    });

    myButton.on('tap', function() {
        alert("Event listener attached by .on");
    });

In this way you can add new listeners at any time. You can also combine these approaches, and you can even listen to the same event more than once, if you need to, as shown in the following example:

    @example
    var myButton = Ext.Viewport.add({
        xtype: 'button',
        centered: true,
        text: 'Click me',

        listeners: {
            tap: function() {
                alert('First tap listener');
            }
        }
    });

    myButton.on('tap', function() {
        alert("Second tap listener");
    });

In this case, both defined event listener functions are called, preserving the order they were added in.

Finally, you can specify multiple listeners by using the .on method, similar to using a listener configuration. The following code revisits the previous example that was setting the button's random width:

    @example
    var myButton = Ext.Viewport.add({
        xtype: 'button',
        centered: true,
        text: 'Click me'
    });

    myButton.on({
        tap: function() {
            var randomWidth = 100 + Math.round(Math.random() * 200);

            this.setWidth(randomWidth);
        },
        widthchange: function(button, newWidth, oldWidth) {
            alert('My width changed from ' + oldWidth + ' to ' + newWidth);
        }
    });

## Removing Listeners

Just as we can add listeners at any time, we can remove them too, this time using {@link Ext.mixin.Observable#un un}. In order to remove a listener, we need a reference to its function. In the previous examples we have just passed a function into the listeners object or the .on call, this time we create the function earlier and link it into a variable called *doSomething*.

Since initially we pass the new *doSomething* function into our listeners object, at first the code works like before. Since we eventually add an Ext.defer function that removes the listener after 3 seconds, clicking on the button in the first 3 seconds yields an alert message, after 3 seconds however the listener is removed so nothing happens:

    @example
    var doSomething = function() {
        alert('handler called');
    };

    var myButton = Ext.Viewport.add({
        xtype: 'button',
        text: 'My Button',
        centered: true,

        listeners: {
            tap: doSomething
        }
    });

    Ext.defer(function() {
        myButton.un('tap', doSomething);
    }, 3000);


In the following example we use a button like in previous code samples, but this time we also add a toggle button that adds and removes the tap listener as you toggle it. The listener starts by being disabled, using the toggle button enables and then disables it:

    @example
    var myButton = Ext.Viewport.add({
        xtype: 'button',
        centered: true,
        text: 'Listener Disabled'
    });

    var handler = function() {
        alert('listener called');
    };

    Ext.Viewport.add({
        xtype: 'togglefield',
        docked: 'bottom',
        label: 'Toggle Listener',

        listeners: {
            change: function(field, thumb, enabled) {
                if (enabled) {
                    myButton.on('tap', handler);
                    myButton.setText('Listener Enabled');
                } else {
                    myButton.un('tap', handler);
                    myButton.setText('Listener Disabled');
                }
            }
        }
    });

## Listener Options

There are a number of additional options that you can pass into listeners.

### Scope

Scope sets the value of *this* inside your handler function. By default *this* is set to the instance of the class firing the event, which is often (but not always) the functionality that you want. This functionality allowed us to call *this.hide()* to hide the button in the second example at the start of this guide.

In the following example we create a Button and a Panel, then listen to the Button's 'tap' event with the handler running in Panel's scope. In order to do this we need to pass in an object instead of a handler function - this object contains the function plus the scope:

    @example
    var myButton = Ext.Viewport.add({
        xtype: 'button',
        centered: true,
        text: 'Click me'
    });

    var panel = Ext.create('Ext.Panel', {
        html: 'Panel HTML'
    });

    myButton.on({
        tap: {
            scope: panel,
            fn: function() {
                alert("Running in Panel's scope");
                alert(this.getHtml());
            }
        }
    });

When you run this example, the value of *this* in the tap handler is the Panel. To see this we have set the Panel's {@link Ext.Panel#cfg-html html} configuration to 'Panel HTML' and then alerted this.getHtml() in our handler. When  the button is tapped we do indeed see the Panel's html being alerted.

### Single

Sometimes we only want to listen to an event one time. The event itself might fire any number of times, but we only want to listen to it once. The following codes illustrates this situation:

    @example
    var myButton = Ext.Viewport.add({
       xtype: 'button',
       centered: true,
       text: 'Click me',

       listeners: {
           tap: {
               single: true,
               fn: function() {
                   alert("I will say this only once");
               }
           }
       }
    });

### Buffer

For events that are fired many times in short succession, we can reduce the number of times our listener is called by using the buffer configuration. In this case our button's tap listener is only invoked once every 2 seconds, regardless of how many times you click it:

    @example
    var myButton = Ext.Viewport.add({
       xtype: 'button',
       centered: true,
       text: 'Click me',

       listeners: {
           tap: {
               buffer: 2000,
               fn: function() {
                   alert("I will say this only once every 2 seconds");
               }
           }
       }
    });

## Firing Your Own Events

Firing your own events is done by calling {@link Ext.mixin.Observable#fireEvent fireEvent} with any event name. In the following example we fire an event called *myEvent* that passes two arguments - the button itself and a random number between 1 and 100:

    @example
    var myButton = Ext.Viewport.add({
       xtype: 'button',
       centered: true,
       text: "Just wait 2 seconds",

       listeners: {
           myEvent: function(button, points) {
               alert('myEvent was fired! You score ' + points + ' points');
           }
       }
    });

    Ext.defer(function() {
        var number = Math.ceil(Math.random() * 100);

        myButton.fireEvent('myEvent', myButton, number);
    }, 2000);

Once again we used Ext.defer to delay the function that fires our custom event, this time by 2 seconds. When the event is fired, the myEvent listener picks up on it and displays the arguments we passed in.

## Conclusions

Events are a key part of Sencha Touch and keep your app informed of changes in the system. If you are new to Sencha Touch we suggest reading the following guides to get a better understanding of how everything works:

* <a href="#!/guide/components">Understanding Components</a>
* <a href="#!/guide/class_system">Understanding the Class System</a>
