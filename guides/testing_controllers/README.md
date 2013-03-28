# Unit testing MVC Controllers

## I. Overview

Unit testing Controllers is not an easy thing, because in any case beyond
trivial it looks more like integration testing that involves many components at
once. It is important to try and simplify testing process as much as possible,
breaking the component interaction down to smallest reasonable pieces to ensure
easier debugging when tests fail.

The most important parts of a Controller are its refs and component selectors;
it is crucial to ensure these selectors are tested properly. Selectors also are
one of the hardest things to test, because they rely on existence and particular
layout of components they select.

## II. Testing refs

Suppose that we have View and Controller:

    Ext.define('MyApp.view.MyView', {
        extend: 'Ext.panel.Panel',
        alias: 'widget.myview',

        dockedItems: [{
            xtype: 'button',
            text: 'OK',
            dock: 'bottom'
        }, {
            xtype: 'button',
            text: 'Cancel',
            dock: 'bottom'
        }],

        ...
    });

    Ext.define('MyApp.controller.MyController', {
        extend: 'Ext.app.Controller',
        
        views: [
            'MyView'
        ],
    
        refs: [{
            ref: 'myView', selector: 'myview'
        }, {
            ref: 'myViewButtonOk',
            selector: 'myview > button[text=OK]'
        }, {
            ref: 'myViewButtonCancel',
            selector: 'myview > button[text=Cancel]'
        }],
    
        init: function() {
            this.control({
                'myview > button': {
                    click: 'onMyViewButtonClick'
                }
            });
        }
    
        onMyViewButtonClick: function(button) {
             ...
        }
    });

For this simplified example of a test suite we will use Jasmine framework. This
is how our test spec may look like:

    describe('MyController refs', function() {
        var view = new MyApp.view.MyView({ renderTo: Ext.getBody() }),
            ctrl = new MyApp.controller.MyController();
    
        it('should ref MyView objects', function() {
            var cmp = ctrl.getMyView();
        
            expect(cmp).toBeDefined();
        });
    
        it('should ref MyView button OK', function() {
            var btn = ctrl.getMyViewButtonOk();
        
            expect(btn.text).toBe('OK');
        });
    
        it('should ref MyView button Cancel', function() {
            var btn = ctrl.getMyViewButtonCancel();
        
            expect(btn.text).toBe('Cancel');
        });
    });

This test suite is simplified to be easier to understand; it can be further
shortened by auto-generating ref tests against controller's refs array, etc.
However the idea remains the same: we take instantiated View and Controller and
run over all the refs, comparing returned objects to our expectations.

## III. Testing `control` component selectors

Taking the same View/Controller setup, we can now add a spec to test component
selectors:

    describe('MyController component selectors', function() {
        var view = new MyApp.view.MyView({ renderTo: Ext.getBody() }),
            ctrl = new MyApp.controller.MyController();
    
        it('should initialize', function() {
            ctrl.init();
        });
    
        it('should control MyView button click events', function() {
            spyOn(ctrl, 'onMyViewButtonClick');
        
            view.down('button[text=OK]').fireEvent('click');
        
            expect(ctrl.onMyViewButtonClick).toHaveBeenCalled();
        });
    });

Note that in our application controller's `init` method will be called
automatically but in our test suite we have to do it manually. An empty spec
will work just fine and will always pass.

This approach may not be feasible for larger applications and bigger Views; in
that case it may be beneficial to create mockup components that simulate parts
of the component layout without adhering strictly to visual design. In fact, the
test View above may be seen as an example of such mockup for real world View.

## IV. Testing event domain selectors

Event domains are a new concept introduced in Ext JS 4.2; they allow passing
information between application components without explicitly calling object
methods. Using them is very easy:

    Ext.define('MyApp.controller.MyController', {
        extend: 'Ext.app.Controller',
        
        init: function() {
            this.listen({
                // This domain passes events between Controllers
                controller: {
                    // This selector matches any Controller
                    '*': {
                        fooevent: 'onFooEvent'
                    }
                }
            });
        },
        
        onFooEvent: function() {}
    });

After initializing `MyController` instance, we can just fire `fooevent` in any
other Controller instance to execute `onFooEvent` method with supplied
arguments. Testing this configuration is equally easy:

    describe('MyController event domain selectors', function() {
        var ctrl = new MyApp.controller.MyController();
        
        it('should listen to fooevent in controller domain', function() {
            spyOn(ctrl, 'onFooEvent');
            
            ctrl.fireEvent('fooevent');
            
            expect(ctrl.onFooEvent).toHaveBeenCalled();
        });
    });

Notice how we fired `fooevent` on the same Controller that is supposed to listen
to this event? That is one of the side effects of how event domains work, and it
is very useful for testing. However it won't help when we want to listen to
`fooevent` fired not in any Controller but in a particular one:

    Ext.define('MyApp.controller.MyController', {
        extend: 'Ext.app.Controller',
        
        init: function() {
            this.listen({
                controller: {
                    '#MyOtherController': {
                        fooevent: 'onMyOtherControllerFooEvent'
                    }
                }
            });
        },
        
        onMyOtherControllerFooEvent: function() {}
    });
    
    Ext.define('MyApp.controller.MyOtherController', {
        extend: 'Ext.app.Controller',
        
        someMethod: function() {
            this.fireEvent('fooevent');
        }
    });

In this case we will have to mock `MyOtherController` class in our test suite,
to avoid instantiating it and bringing on its dependencies:

    describe('MyController event domain selectors', function() {
        var ctrl = new MyApp.controller.MyController();
        
        it('should listen to fooevent from MyOtherController', function() {
            spyOn(ctrl, 'onMyOtherControllerFooEvent');
            
            new Ext.app.Controller({
                id: 'MyOtherController'
            }).fireEvent('fooevent');
            
            expect(ctrl.onMyOtherControllerFooEvent).toHaveBeenCalled();
        });
    });

This mockup works because Controller's `id` defaults to the last part of its
class name, unless specifically overridden.

Besides other Controllers' events, it is possible to `listen` to Stores',
Ext.Direct Providers' and global events. See {@link Ext.app.Controller#listen}
for more detail on how to use event domains; testing them is similar to
Controller event domain.