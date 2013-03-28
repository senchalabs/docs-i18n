# Ext JS 4 by Example: Task management application

## I. Introduction

Ext JS is a Javascript framework for creating rich web applications supporting browsers all the way from IE 6 to the latest versions of Chrome. It it a large framework consisting of many parts, including application management, layouts, theming, build tools and a large number of components from buttons and form elements to grids, trees and charts.

Looking at the framework for the first time can be a daunting task due to its size and complexity. Although Ext JS is distributed with hundreds of examples and many guides, understanding how all the components fit together in a large application can be difficult, especially for new developers.

In this tutorial we will guide you to build a Task Management application from scratch. We assume you are already familiar with HTML, CSS and Javscript, but *not* the Ext JS framework. Perhaps you have used simple DOM based Javascript frameworks such as jQuery or Mootools.

We will show you the typical workflow an experienced developer would follow when creating a new Ext JS project.

Below is a _30 second video_ demonstrating the application we will build. You can interact with a live version of the finished application by clicking _here_. You can download a zip file with the completed application _here_, or checkout the Git repository and follow along _here_.

## II. Getting Started

### 1. Setup, Download and Installation

**Copy section 2.1 from the Ext4 Getting Started Guide**

### 2. Create your application structure

Before we begin writing code, we'll need to create a directory for our project and copy the relevant resources from the Ext JS distribution. We'll also create empty directories to organize the code we'll be writing.

  1. Create a new folder called `tasks_tutorial` accessible by your local web server.
  2. Create the following directories inside `tasks_tutorial`
  3. Copy the following files from your Ext 4 distribution
  4. Create your index.html file

You now have your application skeleton. All new Ext projects should begin this way.

### 3. The Ext class

`Ext` is the only global variable used by Ext JS. It serves as a namespace containing all the Ext components and classes. It also contains a number of alias methods for common functions. For example `Ext.getCmp` is an alias for `Ext.ComponentManager.get`. Related classes are often grouped within namespaces on the Ext object, for example you'll find all the charting related classes in the Ext.chart namespace.

### 3. Define the Application

Every Ext JS 4 application starts with an instance of the Ext.app.Application class. The Application contains global settings for your application (such as the app's name), as well as maintaining references to all of the models, views and controllers used by the app.

In the root project folder `tasks_tutorial`, create a file called `app.js`:

    Ext.application({
        name: 'Tasks'
    });

This will create a global variable called `Tasks` under which all of our application's code will reside. This keeps the global namespace clear which will ease integration with any other Javascript libraries we may wish to use in the future.

### 4. Define the Viewport

The Viewport contains everything visible inside the browser window. By default, an Ext application will look for a file inside the app/view directory called `Viewport.js` which contains the viewport configuration. Think of it as the root container of everything you see in your browser window.

Create a file called Viewport.js inside app/views with the following content:

    Ext.define('Tasks.view.Viewport', {
        extend: 'Ext.container.Viewport',
        html: 'Tasks Application'
    });

At this point we'll also need to include a line in our app.js file to render the viewport automatically when the application starts up:

    Ext.application({
        name: 'Tasks',
        autoCreateViewport: true
    });

We now have all the pieces in place to display something in the browser. Fire up your favorite development browser (we recommend the latest version of Google Chome) and navigate to the root folder of your application (eg http://127.0.0.1/tasks_tutorial). You should see a white screen with the text 'Tasks Application' at the top left:

    SCREENSHOT

## III Displaying a List of Tasks

### 1. Models

Our ultimate goal is to create an application that allows users to create, update and manage a list of tasks. First of all, we need to define what a 'task' actually is as well as the information that will be associated with one. In Ext JS we call this 'Modeling' our data. A Model is a representation of an object and contains a collection of attributes called 'fields'.

For our purposes, a task should have the following fields:

- Title (eg Do Laundry)
- Category (eg Chores)
- Due date (eg 06/08/2011)
- Status (eg Incomplete)

Lets start by creating a new file in app/model called Task.js:

    Ext.define('Tasks.model.Task', {
        extend: 'Ext.data.Model',
        fields: [
            { name: 'title' },
            { name: 'category' },
            { name: 'dueDate', type: 'date'},
            { name: 'status' }
        ]
    });

Great! But before we can create a new task, we'll need somewhere to store it. In Ext, a Store encapsulates a collection of Model objects.

Lets create a new Store for our Tasks and put it in app/store/Tasks.js:

    Ext.define('Tasks.store.Tasks', {
        extend: 'Ext.data.Store',
        model: 'Tasks.model.Task',

        proxy: {
            id  : 'tasksStore',
            type: 'memory'
        },

        data: [
            { title: 'Laundry', category: 'Chores', dueDate: '01/06/2011', status: 'complete' },
            { title: 'Science project', category: 'School', dueDate: '02/06/2011', status: 'incomplete' },
            { title: 'History assignment', category: 'School', dueDate: '03/06/2011', status: 'incomplete' }
        ]
    });

A `proxy` defines _where_ we want to store the data. This could be in memory (as in this case) or we could store the data on a server, or perhaps in LocalStorage (on modern, HTML5 compliant browsers). We'll look at proxies in more detail later.

As you can see we've pre-populated our Store with some dummy data. Later on, we'll replace this with live data.

### 2. Views

Ok, so we modeled a task and created a store with some dummy data. Lets see if we can view our data in a grid.

In Ext JS, view related code is stored in the app/view directory. We usually group views in directories relating to their Controller (which we'll talk about shortly).

Lets create a file called app/views/tasks/List.js:

    Ext.define('Tasks.view.tasks.List', {
        extend: 'Ext.grid.Panel',
        alias: 'widget.taskList',
        store: 'Tasks',

        title: 'My Tasks',

        columns: [
            { header: 'Done',     dataIndex: 'status', width: 50, align: 'center', resizable: false },
            { header: 'Title',    dataIndex: 'title',  flex: 1 },
            { header: 'Category', dataIndex: 'email'           },
            { header: 'Due Date', dataIndex: 'dueDate'         }
        ]
    });

Here we define a Grid and tie it to our Tasks store. That is where the data will be fetched from when it is time to render the grid. We defined some columns and associated them with their corresponding data index (the field name from the Task model).

Lets update Viewport.js and replace our dummy text with our newly defined tasks grid:

    Ext.define('Tasks.view.Viewport', {
        extend: 'Ext.container.Viewport',
        layout: 'fit',

        items: {
            xtype: 'taskList'
        }
    });

An `xtype` is a shorthand way of referring to a component. All of the components that ship with Ext JS have an xtype. Examples include `button`, `panel`, `grid`, `checkbox` and `chart`. We assigned an xtype of `taskList` to our List view with the line `alias: 'widget.taskList'`.

Containers in Ext JS have an `items` config option which list a single child object or array of child objects. For now we just want to display our Grid of tasks, so we use the singular form.

### 3. Controllers

Controllers are the glue that binds an application together. They respond to events and take some action. These events might be, for example, a user clicks a button or the browser window is resized. The actions taken usually involve updating the view of the application, for example popping up a message box or resizing a component.

We'll need to create a Controller so that our application is aware of our model, store and view. Later we'll extend this controller to take actions on events, such as when we click a 'New Task' button.

Create a file app/controller/Tasks.js:

    Ext.define('Tasks.controller.Tasks', {
        extend: 'Ext.app.Controller',

        models: ['Task'],
        stores: ['Tasks'],

        views: ['tasks.List']
    });

We'll also need to make our application aware of this controller. Update app.js:

    Ext.application({
        name: 'Tasks',
        autoCreateViewport: true,

        controllers: ['Tasks']
    });

OK! We should now be able to refresh our browser window and see a list of tasks nicely formatted in an Grid Panel.

## IV. Adding a New Task

### 1. Create a New Task button

Lets add a button we can use to create new tasks. We'll put this in a toolbar at the top of the Tasks grid. Open up app/view/tasks/List.js and add the following code under the line that read `title: 'Tasks'` (append a comma after `title: 'Tasks'` to keep the javascript valid):

    dockedItems: [{
        xtype: 'toolbar',
        dock: 'top',
        items: [
            { xtype: 'button', text: 'New Task' }
        ]
    }]

That will create a toolbar docked to the top of the Grid panel containing a single `button` component. Try refreshing your browser to see the result.

Next we'll want to link this button to an action so something happens when someone clicks it. Open up app/controller/Tasks.js and add the following code under the line that reads `views: ['tasks.List']` (remembering the extra comma):

    init: function() {
        this.control({
            'taskList > toolbar > button': {
                click: this.newTask
            }
        });
    },

    newTask: function() {
        alert('New Task!')
    }

The `control` method attaches listeners to components identified by selectors. The selector targets a component using Ext.ComponentQuery. The interface is very similar to CSS selectors. In the code above, we target the button with the selector `taskList > toolbar > button`. We then attach a listener to the `click` event and bind it to the `newTask` function.

The result is that when the button is clicked an alert box is created with the next 'New Task!'.

### 2. Display a New Task form

We want to create a popup that displays a form with fields for creating a new task when a user clicks the 'New Task' button. Create a new file `app/view/tasks/Form.js`:

    Ext.define('Tasks.view.tasks.Form', {
        extend: 'Ext.window.Window',
        alias: 'widget.newTask',

        title: 'New Task',
        resizable: false,

        bodyPadding: 20,
        html: 'New Task Window'
    });

Update the `views` config option in `app/contoller/Tasks.js` to include this new view:

    views: [
        'tasks.List',
        'tasks.Form'
    ],

Also update `app/controller/Tasks.js` to show the window when the user clicks the New Task button:

    newTask: function() {
        Ext.create('Tasks.view.tasks.Form').show();
    }

Try refreshing your browser to test it out. Now lets replace the html content with some fields. In `app/view/tasks/Form.js`, replace these lines:

    bodyPadding: 20,
    html: 'New Task Window'

with the following:

    items: {
        xtype: 'form',
        bodyPadding: 10,
        border: false,
        items: [
            {
                xtype: 'textfield',
                fieldLabel: 'Title',
                name: 'title'
            },
            {
                xtype: 'textfield',
                fieldLabel: 'Category',
                name: 'category'
            },
            {
                xtype: 'datefield',
                fieldLabel: 'Due Date',
                name: 'dueDate'
            }
        ]
    },

This creates a form with the 3 new fields required to add a new task. The `layout: 'fit'` config option means the form will automatically stretch if the New Task window is resized. Test it out in your browser.

### 3. Storing the new task

Lets create 'Save' and 'Cancel' buttons on our New Task form. Edit `app/view/tasks/Form.js` and add this code below your `items`:

    buttons: [
        {
            text: 'Save',
            action: 'save'
        },
        {
            text: 'Cancel',
            action: 'cancel'
        }
    ]

We've defined `action` parameters on our buttons which we will use to target these components. Lets make the cancel button close the window. Edit `app/view/controller/Tasks.js` and add this code to your `this.control` method:

    'newTask button[action=cancel]': {
        click: function(btn) {
            btn.up('newTask').close();
        }
    }

When the user clicks the Cancel button, the click handler is run. The click handler is passed a reference to the 'Cancel' button. We use the `up` method on this component to find the closest ancestor with an id of `newTask`, which happens to be the New Task window. Now that we have a reference we can call `close` to close the New Task window.

Now lets add a handler for the Save button. Add this code under the code you just wrote (don't forget your commas!):

    'newTask button[action=save]': {
        click: this.saveTask
    }

Now lets add the `saveTask` method at the bottom of our Tasks controller:

    saveTask: function() {
        console.log(this.getNewTaskForm().getValues());
    }

Wondering where that `getNewTaskForm()` method came from? As it happens it doesn't exist yet, so lets create it. Under you `views` config option, add this code:

    refs: [
        {
            ref: 'newTaskForm',
            selector: 'newTask > form'
        }
    ]

The `refs` config option exists so you can define references to components on the page. This will create a helper method, in this case `getNewTaskForm` which is available from methods on your controller by calling `this.getNewTaskForm()`. So now we have an easy way of getting a reference to our New Task form from any method in our Tasks controller.

Save your work and refresh your browser. When you click the 'Save' button on the New Task form you should see an object containing the values from the form in your browser's console window.

Now we just need to create a new Task from these values and add them to our Tasks store. Edit the `saveTask` method like this:

    saveTask: function(btn) {
        var task = Ext.create('Tasks.model.Task', this.getNewTaskForm().getValues()),
            store = this.getStore('Tasks');

        store.add(task);
        btn.up('newTask').close();
    }

Refresh your browser and try adding a task. You should see it get added to the grid when you click Save.

### 4. Validating your fields

One problem with the New Task form is that values can be left blank. Lets add some validations so that each field must be filled in.

Add the following code under your `fields` in `app/model/Task.js`:

    validations: [
        { type: 'presence', name: 'title' },
        { type: 'presence', name: 'category' },
        { type: 'presence', name: 'dueDate' }
    ]

This adds validations to the Task model. These validations check for the presence of the `title`, `category` and `dueDate` fields.

Now we need to update our `saveTask` method to check the Task is valid before adding it to the Store. If it is *not* valid, we need to show the error messages on the form. Update the `saveTask` method to the following:

    saveTask: function(btn) {
        var formValues = this.getNewTaskForm().getValues(), // A hash of
            task = Ext.create('Tasks.model.Task', formValues),
            store = this.getStore('Tasks'),
            errors = task.validate(),
            fields = this.getNewTaskForm().getForm().getFields();

        if (errors.isValid()) {
            store.add(task);
            btn.up('newTask').close();
        } else {
            fields.each(function(field) {
                var fieldErrors = errors.getByField(field.getName()),
                    errorMessages = Ext.Array.pluck(fieldErrors, "message");

                field.markInvalid(errorMessages);
            });
        }
    }

We also want our error messages to appear underneath the fields by default. Add these lines to the `items` config in `app/views/tasks/Form.js`

    fieldDefaults: {
        msgTarget: 'under'
    }

Try refreshing your browser and adding an invalid task to see the result.

## V. Marking a Task Complete

### 1. Adding a Checkbox






























