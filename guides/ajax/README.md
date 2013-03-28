# Using AJAX with Sencha Touch

Sencha Touch provides a variety of convenient ways to get data into and out of your application. All of the data-bound
Components such as Lists, Nested Lists, and DataViews use Stores, which are easily configured to fetch and save data to a large variety of sources. We will look at managing data with stores later on, but first let us start with how to generate simple AJAX requests.

## Simple Requests with Ext.Ajax

Because of browser security restrictions, AJAX requests are usually made to urls on the same domain as your application. For example, if your application is found at http://myapp.com, you can send AJAX requests to urls such as http://myapp.com/login.php and http://myapp.com/products/1.json, but not to other domains such as http://google.com. However, Sencha Touch provides some alternatives to get around this limitation, as shown in the final part of this guide (Cross-Domain Requests and JSON-P).

The following code shows an AJAX request to load data form an url on the same domain:

    Ext.Ajax.request({
        url: 'myUrl',
        callback: function(options, success, response) {
            console.log(response.responseText);
        }
    });

Assuming that your app is on http://myapp.com, the previous code sends a GET request to http://myapp.com/myUrl. Since AJAX calls are asynchronous, once the response comes back, the callback function is called with the response. In this example the callback function logs the contents of the response to the console when the request has finished. 

### AJAX Options

Ext.Ajax takes a wide variety of options, including setting the method (GET, POST, PUT, or DELETE), sending headers, and setting params to be sent in the url. The following code sets the method such that a POST request is sent instead of a GET:

    Ext.Ajax.request({
        url: 'myUrl',
        method: 'POST',

        callback: function(options, success, response) {
            console.log(response.responseText);
        }
    });

Sending parameters done as shown in the following example:

    Ext.Ajax.request({
        url: 'myUrl',

        params: {
            username: 'Ed',
            password: 'not a good place to put a password'
        },

        callback: function(options, success, response) {
            console.log(response.responseText);
        }
    });

When setting parameters like in this example, the request is automatically sent as a POST with the params object sent as form data. The previous request above is like submitting a form with username and password fields.

If we wanted to send this as a GET request instead, we would have to specify the method again, in which case our params are automatically escaped and appended to the url:

    Ext.Ajax.request({
        url: 'myUrl',
        method: 'GET',

        params: {
            username: 'Ed',
            password: 'bad place for a password'
        },

        callback: function(options, success, response) {
            console.log(response.responseText);
        }
    });

The previous code sample generates the following request:

    http://mywebsite.com/myUrl?_dc=1329443875411&username=Ed&password=bad%20place%20for%20a%20password

You may have noticed that our request created an url that contained "_dc=1329443875411". When you make a GET request like this, many web servers cache the response and send you back the same response every time you make the request.
Although this speeds the web up, it is not always what you want. In fact in applications this is rarely what you want, so we "bust" the cache by adding a timestamp to every request. This tells the web server to treat it like a fresh, uncached request.

If you want to turn this behavior off, we ccould set disableCaching to false, as shown in the following code sample:

    Ext.Ajax.request({
        url: 'myUrl',
        method: 'GET',
        disableCaching: false,

        params: {
            username: 'Ed',
            password: 'bad place for a password'
        },

        callback: function(options, success, response) {
            console.log(response.responseText);
        }
    });

Since the request no longer contains the cache busting string, it looks like the following string:

    http://mywebsite.com/myUrl?username=Ed&password=bad%20place%20for%20a%20password

### Sending Headers

Another option related to customizing the request is the headers option. This enables you
to send any custom headers to your server, which is often useful when the web server returns different content based on these headers. For example, if your web server returns JSON, XML, or CSV based on the header it is passed, we can request JSON like in the following example:

    Ext.Ajax.request({
        url: 'myUrl',

        headers: {
            "Content-Type": "application/json"
        },

        callback: function(options, success, response) {
            console.log(response.responseText);
        }
    });

If you create a request like this and inspect it in Firebug/web inspector, you will see that the Content-Type header has been set to application/json. Your web server can pick up on this and send you the right response. You can pass any number of headers you like into the headers option.

### Callback Options

Not every AJAX request succeeds: sometimes the server is down, or your internet connection drops, or something else bad happens. Ext.Ajax allows you to specify separate callbacks for each of these cases:

    Ext.Ajax.request({
        url: 'myUrl',

        success: function(response) {
            console.log("Spiffing, everything worked");
        },

        failure: function(response) {
            console.log("Curses, something terrible happened");
        }
    });

These do exactly what you would expect them to do, and hopefully most of the time it is your success callback that gets called. It is pretty common to provide a success callback that updates the UI or does whatever else is needed by the application flow, and a failure handler that either resends the request or alerts the user that something went wrong.

You can provide *success*/*failure* and *callback* at the same time, so for this request the *success* function is called first (if everything was ok), followed by the main *callback* function. In the case of failure, the *failure* function is called first, followed by *callback*:

    Ext.Ajax.request({
        url: 'myUrl',

        success: function(response) {
            console.log("Spiffing, everything worked");
        },

        failure: function(response) {
            console.log("Curses, something terrible happened");
        },

        callback: function(options, success, response) {
            console.log("It is what it is");
        }
    });

### Timeouts and Aborting Requests

Another way requests can fail is when the server took too long to respond and the request timed out. In this case your
*failure* function is called, and the request object it is passed has the timedout variable set to true:

    Ext.Ajax.request({
        url: 'myUrl',

        failure: function(response) {
            console.log(response.timedout); // logs true
        }
    });

By default the timeout threshold is 30 seconds, but you can specify it for every request by setting the timeout value in millisecond. In the following case the request will give up after 5 seconds:

    Ext.Ajax.request({
        url: 'myUrl',
        timeout: 5000,

        failure: function(response) {
            console.log(response.timedout); // logs true
        }
    });

It is also possible to abort requests that are currently outstanding. In order to do this, you need to save a reference to the request object that Ext.Ajax.request returns, as shown in the following code sample:

    var myRequest = Ext.Ajax.request({
        url: 'myUrl',

        failure: function(response) {
            console.log(response.aborted); // logs true
        }
    });

    Ext.Ajax.abort(myRequest);

This time the failure callback is called and its response.aborted property is set. You can use all of the error handling above in your apps:

    Ext.Ajax.request({
        url: 'myUrl',

        failure: function(response) {
            if (response.timedout) {
                Ext.Msg.alert('Timeout', "The server timed out :(");
            } else if (response.aborted) {
                Ext.Msg.alert('Aborted', "Looks like you aborted the request");
            } else {
                Ext.Msg.alert('Bad', "Something went wrong with your request");
            }
        }
    });

## Cross-Domain Requests

A relatively new capability of modern browsers is called <a href="http://www.w3.org/TR/cors/">CORS</a>, which stands
for Cross-Origin Resource Sharing. This allows you to send requests to other domains, without the usual security
restrictions enforced by the browser. Sencha Touch provides support for CORS, although you will probably need to 
configure your web server in order to enable it. If you are not familiar with the required actions for setting up your web server for CORS, a quick web search should give you plenty of answers.

Assuming your server is set up, sending a CORS request is easy:

    Ext.Ajax.request({
        url: 'http://www.somedomain.com/some/awesome/url.php',
        withCredentials: true,
        useDefaultXhrHeader: false
    });

## Form Uploads

The final topic covered in this guide is uploading forms, a functionality that is illustrated in the following sample code:

    Ext.Ajax.request({
        url: 'myUrl',
        form: 'myFormId',

        callback: function(options, success, response) {
            if (success) {
                Ext.Msg.alert('Success', 'We got your form submission');
            } else {
                Ext.Msg.alert('Fail', 'Hmm, that did not work');
            }
        }
    });

This finds a &lt;form&gt; tag on the page with id="myFormId", retrieves its data and puts it into the request params object, as shown at the start of this guide. Then it submits it to the specified url and calls your callbacks like normal.
