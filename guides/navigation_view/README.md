# Using Navigation View in Sencha Touch

Navigation View is a new component in Sencha Touch. At its base, it is a simple container with a card layout and a docked toolbar; however on top of that we built an easy way to push (add) and pop (remove) views in a stack-like manner. When you push a view, it adds that view into the `stack` and animates the new title into the toolbar. It  also animates a back button into the toolbar, so you can return (or pop) to the previous view.

The following example demonstrates this functionality:

    @example preview
    //create the navigation view and add it into the Ext.Viewport
    var view = Ext.Viewport.add({
        xtype: 'navigationview',

        //we only give it one item by default, which will be the only item in the 'stack' when it loads
        items: [
            {
                //items can have titles
                title: 'Navigation View',
                padding: 10,

                //inside this first item we are going to add a button
                items: [
                    {
                        xtype: 'button',
                        text: 'Push another view!',
                        handler: function() {
                            //when someone taps this button, it will push another view into stack
                            view.push({
                                //this one also has a title
                                title: 'Second View',
                                padding: 10,

                                //once again, this view has one button
                                items: [
                                    {
                                        xtype: 'button',
                                        text: 'Pop this view!',
                                        handler: function() {
                                            //and when you press this button, it will pop the current view (this) out of the stack
                                            view.pop();
                                        }
                                    }
                                ]
                            });
                        }
                    }
                ]
            }
        ]
    });

## Creating a Simple Navigation View

Creating a Navigation View is similar to creating other containers. You use {@link Ext.ClassManager#create} to create your Navigation View instance, and the only configuration you need to add is {@link Ext.navigation.View#cfg-items items}.

    @example
    var view = Ext.create('Ext.navigation.View', {
        fullscreen: true,
        items: [
            {
                title: 'Navigation View',
                html: 'This is the first item in the stack!'
            }
        ]
    });

In this example, we give the Navigation View the following two configurations:

* **{@link Ext.navigation.View#cfg-fullscreen fullscreen}**: This ensures that it is automatically inserted into the {@link Ext.Viewport viewport}.
* **{@link Ext.navigation.View#cfg-items items}**: This represents the items that the navigation view contains. Since we only insert one item here, it the first item in the `stack` and is therefore active.
    * **title**: This is the title that is displayed in the navigation bar (not required).
    * **{@link Ext.Container#html html}**: We use the `html` configuration here for providing some dummy content.

## Pushing New Views

To 'push' means to add a new view to the stack of the navigation view. Pushing will trigger the following three actions:

1. Animate the navigation view to show the new item (slide).
2. Animate the `title` configuration of the item (if specified) in the navigation bar (slide).
3. Animate the back button into the navigation bar (slide).

Pushing views is done using the {@link Ext.navigation.View#method-push push} method:

    view.push({
        title: 'New views title',
        html: 'Some content'
    });

You can either pass a reference to a component or use a configuration like in the previous example. And of course, the item you push can be any subclass of {@link Ext.Component}:

    var tabPanel = Ext.create('Ext.tab.Panel', {
        items: [
            {
                title: 'First',
                html: 'first'
            },
            {
                title: 'Second',
                html: 'second'
            }
        ]
    });

    view.push(tabPanel);

## Popping Views

To 'pop' means to remove the topmost (the visually active) view from the Navigation View. Of course you need to have more than one item in the stack for this to operation to have have any result. When you pop a view, it perform the following actions:

1. Animate the navigation view back to the previous item in the stack (reverse slide).
2. Animate the current title out of view, and animate the previous stacks title into view (slide).
3. Animate the back button out of view, and if there is still more than two items in the stack, animate the back button of the previous view.

Popping views is done by calling the {@link Ext.navigation.View#method-pop pop} method:

    view.pop();
