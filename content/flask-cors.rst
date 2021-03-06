Flask configuration to enable CORS
##################################

:date: 2013-07-08 22:55
:tags: flask, cors, rest
:category: flask
:slug: flask-cors
:author: Christophe Serafin
:lang: en

When you start using Javascript framework, you will quickly face some CORS issue. This article will share how we decided to deal with cross domain browser restriction in our own application called Bulleval


What is CORS ?
==============

CORS stands for Cross-origin Resource Sharing. As quoted from wikipedia, it is "a mechanism that allows Javascript on a web page to make XMLHttpRequests to another domain".

Sending an XmlHTTPRequest ("XHR" for short) from one domain to a sub-domain or another will give you the following error message ::

    Origin is not allowed by Access-Control-Allow-Origin

For many years the only solution was to use proxy requests. Fortunately, the CORS mechanism provides us a better solution :

In order to ask if the server domain will allow the request, modern browsers send what we call a preflighted request to the server - using HTTP Verb OPTIONS - before sending the actual request.

HTTP Response will have to contain specific headers to allow the actual request:

.. code-block:: js

    Access-Control-Allow-Origin : http://your-client-side-domain/
    Access-Control-Allow-Methods : GET, POST, PUT, DELETE


How we deal with crossdomain request on our Flask server ?
==========================================================

Context
-------

At first, we used Flask decorator snippet (http://flask.pocoo.org/snippets/56/) to enable CORS on our web services.

Unfortunately, when you try to access an unknown URL, the server will return a 404 (NOT Found) to your OPTIONS request.

We chose to allow crossdomain request on every routes and let our custom decorator verify user access.


HTTP Headers
------------

We decided to use @app.before_request decorator and @app.after_request to modify HTTP response headers

Here is what we came up with :

.. code-block:: python

    @app.before_request
    def option_autoreply():
        """ Always reply 200 on OPTIONS request """

        if request.method == 'OPTIONS':
            resp = app.make_default_options_response()

            headers = None
            if 'ACCESS_CONTROL_REQUEST_HEADERS' in request.headers:
                headers = request.headers['ACCESS_CONTROL_REQUEST_HEADERS']

            h = resp.headers

            # Allow the origin which made the XHR
            h['Access-Control-Allow-Origin'] = request.headers['Origin']
            # Allow the actual method
            h['Access-Control-Allow-Methods'] = request.headers['Access-Control-Request-Method']
            # Allow for 10 seconds
            h['Access-Control-Max-Age'] = "10"

            # We also keep current headers
            if headers is not None:
                h['Access-Control-Allow-Headers'] = headers

            return resp


    @app.after_request
    def set_allow_origin(resp):
        """ Set origin for GET, POST, PUT, DELETE requests """

        h = resp.headers

        # Allow crossdomain for other HTTP Verbs
        if request.method != 'OPTIONS' and 'Origin' in request.headers:
            h['Access-Control-Allow-Origin'] = request.headers['Origin']


        return resp


You want to improve this solution / Have a better idea ?
========================================================

I would be glad to hear from you !
Drop us an email at : thekids@coalkids.com