# Using the Data Package in Sencha Touch

The data package is responsible for loading and saving all data in a Sencha Touch app. Most of the actions involving the data package are related to models, stores, and proxies:

* **Model:** A model represents an entity your app cares about. User, Contact, Address, and Product could all be models. At its simplest, a model is just a collection of fields and their data, but they can do a lot more.
* **Store:** A store is a collection of model instances. Mostly it is just a glorified array, but it also provides capabilities like sorting, filtering, and grouping, and it is capable of firing useful events.
* **Proxy:** A proxy is responsible for the actual loading and saving of data into a store. Usually you will create an {@link Ext.data.proxy.Ajax AJAX proxy} which fetches data from your server and uses it to populate a store.

Find out more about <a href="#!/guide/models">Models</a>, <a href="#!/guide/stores">Stores</a> and <a href="#!/guide/proxies">Proxies</a> in their individual guides.

## Further Reading

Data is only a part of the Sencha Touch ecosystem. To understand more about the framework and how it works, we recommend reading the following guides:

* [Components and Containers](#!/guide/components)
* [The Class System](#!/guide/class_system)
* [The Layout System](#!/guide/layouts)
* [Getting Started](#!/guide/getting_started)
