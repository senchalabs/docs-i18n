# Upgrading from Sencha Touch 1.x to 2.x

Sencha Touch 2 brings a number of refinements to the framework. Since some of these changes require you to update parts of your application's code, this guide takes you through the changes you will need to make.

In addition to the guide, Sencha Touch 2 comes with a backwards-compatibility build that makes the migration process easier. **Note that this build does not automatically guarantee that your 1.x app works out of the box**. Wherever possible, the compatibility build figures out what your 1.x app code is trying to do and route it through to the 2.x API. Whenever it does this, the compatibility build logs a warning to the console indicating the items that you need to update.

If you follow the steps below and then correct any warnings the compatibility layer informs you about, you should obtain an application that runs on Sencha Touch 2.x.

[See the Migrating from Touch 1.x to 2.0 Video from SenchaCon 2011](#!/video/migrating-from-1-to-2)

## Class System

When migrating your app, the biggest change required is related to the way in which classes are defined. Sencha Touch 2 uses the upgraded class system derived from Ext JS 4, which brings powerful new capabilities such as mixins, dynamic loading, and the config system.

In Sencha Touch 1.x, there were two main ways of defining classes - using Ext.extend and using one of the MVC-specific convenience functions, such as regModel and regController. In Sencha Touch 2, all classes are defined the same way, using the Ext.define method. Let us consider the following 1.x example class and see how we could migrate it to the 2.x framework:

    Geo.views.BillSummary = Ext.extend(Ext.Panel, {
        scroll: 'vertical',
        html: "Loading...",
        styleHtmlContent: true,
        initComponent: function() {
            this.tpl = Ext.XTemplate.from('billsummary');
            Geo.views.BillSummary.superclass.initComponent.call(this);
        },

        /**
         * Get the billSummary and update the contents of the panel.
         */
        getBill: function(bill) {
            Geo.CongressService.getBillSummary({
                bill: bill
            }, this.onBillSummaryResponse, this);
        },

        // private
        onBillSummaryResponse: function(billSummary) {
            if (Ext.isArray(billSummary.Paragraph)) {
                this.update(billSummary);
            } else {
                this.update('No Bill Summary Available');
            }

        }
    });

In Sencha Touch 2.x, the previous code is written as follows:

    Ext.define('Geo.view.BillSummary', {
        extend: 'Ext.Panel',

        config: {
            scroll: 'vertical',
            html: 'Loading...',
            styleHtmlContent: true
        },

        initialize: function() {
            this.callParent(arguments);

            this.tpl = Ext.Template.from('billsummary');
        },

        /**
         * Get the billSummary and update the contents of the panel.
         */
        getBill: function(bill) {
            Geo.CongressService.getBillSummary({
                bill: bill
            }, this.onBillSummaryResponse, this);
        },

        // private
        onBillSummaryResponse: function(billSummary) {
            if (Ext.isArray(billSummary.Paragraph)) {
                this.setHtml(billSummary);
            } else {
                this.setHtml('No Bill Summary Available');
            }
        }
    });

The first thing to note is that we have swapped out Ext.extend for Ext.define. All of the old constituent parts are still present, but we have shuffled them around to use the new syntax. You can note that in version 2.x all of the class names are string based. This enables the dynamic loading system to automatically load these classes onto the page, if they are not already present. See the <a href="#!/guide/class_system">class system guide</a> for more details.

Another change relates to moving all of the configuration options into a config object. The configuration options for each class can be found in the class documentation. When defining a class, any parameter found in the configuration section of the class should be placed into a config object.

The config system provides some key benefits, primarily a guarantee of the API's consistency. For example, the {@link Ext.Panel#html html} config option guarantees that we can call {@link Ext.Panel#getHtml getHtml} and {@link Ext.Panel#setHtml setHtml} at any time, removing the guesswork from figuring out which functions to call. Every single config option has getter and setter functions that follow the same pattern as getHtml and setHtml. We use this to our advantage in the onBillSummaryResponse function, where we replaced the old 'update' function with the clearer {@link Ext.Panel#setHtml setHtml} function.

Finally, we replaced initComponent with initialize. In version 1.x of the framework, initComponent was only available on Component classes, which excluded all the other classes, such as Models, Controllers and utilities. In version 2.x, every class has an initialize function that you can implement if you want some logic to be performed on instantiation. Another detail to note here is that you no longer need the long *Geo.views.BillSummary.superclass.initComponent.call(this);* call - you can instead always use *this.callParent(arguments)* to call the superclass function.

## MVC Migration

The MVC architecture in Sencha Touch 2 is a refinement of the approach taken in Sencha Touch 1. Although most of the concepts are the same, the syntax has been improved to make the API more expressive, and to make your code more readable and testable.

### Models

Similar to all other classes in Sencha Touch 2, the Model classes and the rest of the data package now expect their configurations to be placed into a config block. Sencha Touch 2 also unifies the way you define all of your application's classes: Ext.regModel is no longer needed - instead a Model is just {@link Ext#define defined} like any other class. Migrating your Models is very simple - considering you once had a Model like in the following code sample:

    Ext.regModel('MyApp.model.User', {
        fields: [
            {name: 'name',  type: 'string'},
            {name: 'age',   type: 'int'},
            {name: 'alive', type: 'boolean', defaultValue: true}
        ],

        validations: [
            {type: 'presence', field: 'age'},
            {type: 'length',   field: 'name', min: 2}
        ],

        sayName: function() {
            alert(this.get('name'));
        }
    });

In Sencha Touch 2 the same Model would look like the following:

    Ext.define('MyApp.model.User', {
        extend: 'Ext.data.Model',

        config: {
            fields: [
                {name: 'name',  type: 'string'},
                {name: 'age',   type: 'int'},
                {name: 'alive', type: 'boolean', defaultValue: true}
            ],

            validations: [
                {type: 'presence', field: 'age'},
                {type: 'length',   field: 'name', min: 2}
            ]
        },

        sayName: function() {
            alert(this.get('name'));
        }
    });

A Model migration is generally done in four steps:

1. Replace Ext.regModel with Ext.define
2. Create a new Model class by extending Ext.data.Model
3. Move all of your Model's configurations into its config block
4. Leave any custom functions (for example sayName in the previous code) outside of the config block

### Views

Aside from migrating to the new class system syntax, view migration is quite simple. The main point to keep in mind is the convention regarding view class names. In Sencha Touch 2 and onward we recommend that your view class names follow the pattern MyApp.view.SomeViewName. Note that the 'view' is singular, which enables the class system to automatically load the view class from the file app/view/SomeViewName.js.

### Application

When it comes to the Ext.application definition, you can keep most of the syntax from your 1.x app:

    Ext.application({
        name: 'MyApp',

        icon: 'resources/images/logo.png',

        launch: function() {
            Ext.create('MyApp.view.Main');
        }
    });

The only change is that we use Ext.create to instantiate MyApp.view.Main, which is our app's main screen.

In addition to this change, there is another slight change in approach when it comes to loading the models, views, controllers, and stores your application needs. In 1.x, it was common to define all of your controllers inside the Ext.application block, along with *some* of your models, views and stores. The rest of the dependencies could be scattered throughout your application, making it difficult to easily understand exactly what the app is composed of.

In Sencha Touch 2 we encourage you to define all of your application's dependencies inside the Ext.application block, instead of placing some of them inside Controllers. An example might look like this:

    Ext.application({
        name: 'MyApp',

        icon: 'resources/images/logo.png',

        models: ['User', 'Group'],
        controllers: ['Users', 'Login'],
        views: ['Main', 'Users'],
        stores: ['Users'],

        launch: function() {
            Ext.create('MyApp.view.Main');
        }
    });

### Controllers

Similar to Model, Sencha Touch 2 expects any Controller to be defined like any other class, so the Ext.regController function is deprecated. In version 1.x we might have a controller such as the following:

    Ext.regController("searches", {
        showSomething: function() {
            alert('something');
        }
    });

which in version 2.x becomes as shown in the following sample code:

    Ext.define('MyApp.controller.Searches', {
        extend: 'Ext.app.Controller',

        showSomething: function() {
            alert('something');
        }
    });

As mentioned previously, if your 1.x controller defines additional model, view or store dependencies, you should move these into the Application instead. The compatibility build will still attempt to load those dependencies, but the non-compat build will not.

#### Routes

In Sencha Touch 1.x, a Controller was largely a collection of functions that could be dispatched to externally. Often this would mean that an Application would call Ext.dispatch, specifying the Controller name, which function to call and which arguments to pass to the function. Other times, the dispatching would be automatic, triggered by a change in the url picked up by the Router.

In 2.x, the Controller becomes a lot more proactive, actively registering inside its config block routes that it cares about:

    Ext.define('MyApp.controller.Searches', {
        config: {
            routes: {
                'search/:query': 'doSearch'
            }
        },

        doSearch: function(query) {
            alert('searching for ' + query);
        }
    });

This bypasses the need for a separate routes file and allows the Controller to indicate that whenever the page URL matches 'search/:query', the doSearch function will be called with the query. For example, if the page URL is currently http://myapp.com/#searches/abc123, doSearch will be called with 'abc123'. If the page URL later becomes '#searches/sencha', doSearch is called again with 'sencha'.

#### New Capabilities

In Sencha Touch 2 Controllers gained new capabilities, the most powerful of which are refs and control. For more information see the <a href="#!/guide/controllers">controllers guide</a>.

## Further Help

Once you have migrated everything you can and updated all of your code such that there are no more console warnings, your app should be mostly functional. For any specific problems the best place to get help is on the <a href="http://www.sencha.com/forum/forumdisplay.php?89-Sencha-Touch-2.x-Forums">Sencha Touch 2 Forums</a>.
