# Using Lists in Sencha Touch

{@video vimeo 37212149}

Sencha Touch provides a List component which is suited for presenting an index-style list of items. In this tutorial, we walk you through setting up a basic list, and show how to add an index bar and group items together under a marker. We also show you how to create a detail panel to reveal information about each list item.

This tutorial features the new NavigationView and MVC Support built into Sencha Touch.

You can view the source code on GitHub: [http://github.com/senchalearn/Presidents](http://github.com/senchalearn/Presidents)

Live demo: [http://senchalearn.github.com/Presidents/](http://senchalearn.github.com/Presidents/)

## Guide

List is a component that renders a {@link Ext.data.Store Store} as a list of items on the page. List is a subclass of {@link Ext.dataview.DataView DataView}, which provides most of its capabilities (see [DataView guide](#/guide/dataview)). However, a List adds the following capabilities of its own:

* Grouping of items, optional index bar, pinned headers
* Optional disclosure icons on each item
* Optional icons and labels for each item

## Creating a Simple List

You can render a List with static items as follows:

	Ext.create('Ext.List', {
		store: {
			fields: ['name'],
			data: [
				{name: 'Cowper'},
				{name: 'Everett'},
				{name: 'University'},
				{name: 'Forest'}
			]
		},

		itemTpl: '{name}'
	});

This code sample renders one {@link Ext.dataview.component.DataItem DataItem} for each item in the {@link Ext.data.Store Store}. You can also attach listeners to events on the List, as illustrated by the following code:

	Ext.create('Ext.List', {
		listeners: {
			select: function(view, record) {
				Ext.Msg.alert('Selected!', 'You selected ' + record.get('name'));
			}
		}

		// store and itemConfig as before
	});

### Preview

	@example preview
	Ext.create('Ext.List', {
		fullscreen: true,

		store: {
			fields: ['name'],
			data: [
				{name: 'Cowper'},
				{name: 'Everett'},
				{name: 'University'},
				{name: 'Forest'}
			]
		},

		itemTpl: '{name}',

		listeners: {
			select: function(view, record) {
				Ext.Msg.alert('Selected!', 'You selected ' + record.get('name'));
			}
		}
	});



