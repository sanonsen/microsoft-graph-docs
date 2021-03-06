# Get started with Microsoft Graph in a Python app 

This article describes the tasks required to get an access token from Azure AD and call Microsoft Graph. It walks you through [Sending mail via Microsoft Graph from Python](https://github.com/microsoftgraph/python-sample-send-mail) and explains the main concepts that you implement to use the Microsoft Graph API. The article describes how to access Microsoft Graph by using direct REST calls.

![Send mail form](https://raw.githubusercontent.com/microsoftgraph/python-sample-send-mail/master/static/images/sendmail.png)

## Choosing an authentication library

To make calls to Microsoft Graph, your app must obtain a valid access token from Azure Active Directory (Azure AD), the Microsoft cloud identity service, and the token must be passed in an HTTP header with each call to the Microsoft Graph REST API. Graph's approach to authentication is based on the OAuth 2.0 and Open ID Connect standards, so there are many [authentication libraries](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries) that you can choose from to implement authentication in your application.

The sample covered below uses the [Flask-OAuthlib](https://flask-oauthlib.readthedocs.io/en/latest/) library to implement OAuth 2.0 [Authorization Code Grant](https://tools.ietf.org/html/rfc6749#section-4.1) workflow, which is the recommended auth workflow for web applications written in Python. For information about other authentication options, see [Python authentication samples for Microsoft Graph](https://github.com/microsoftgraph/python-sample-auth).

## Installing and running the send-mail sample

To install and configure the sample app, follow the instructions in [Installing the Python REST samples](https://github.com/microsoftgraph/python-sample-auth/blob/master/installation.md). For the send-mail sample covered below, use this command to clone the repo:

```git clone https://github.com/microsoftgraph/python-sample-send-mail.git```

When you register the application as covered in the [installation instructions](https://github.com/microsoftgraph/python-sample-auth/blob/master/installation.md), be sure to include the **User.Read** and **Mail.Send** permissions, which are required for this sample.

After completing the installation/configuration, you can run the sample app by following the instructions for [running the sample](https://github.com/microsoftgraph/python-sample-send-mail#running-the-sample).

## Code walkthrough

The following is an overview of the [source code](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py) for the sample app.

The first few lines of code are the [imports](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L4-L11) for the Python modules and packages used in the sample:

* The **base64** module from the standard library is used for encoding email attachments.
* The **pprint** module from the standard library is used to pretty-print any error messages returned by Graph. (For example, if you try to send to an invalid email address.)
* The **uuid** module from the standard library is used to generate a random 36-character string to uniquely identify each Graph request. This can be useful for debugging purposes.
* The **flask** package is the web framework for the sample.
* The **OAuth** class of **flask_oauthlib.client** is a wrapper around the Flask app that implements the OAuth 2.0 authentication workflow.
* The **config** module contains the application registration settings, as configured in the installation process above.

Next, we [create a Flask app](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L13-L15) and then create the [Graph client object](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L17-L26), named **MSGRAPH**.

After those initial setup steps come three Flask route handler functions that implement the authentication workflow: [homepage()](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L28-L31), [login()](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L33-L37), and [authorized()](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L39-L46). For more information about the authentication workflow, see the [Sample architecture](https://github.com/microsoftgraph/python-sample-auth#sample-architecture) section of the Python authentication samples repo.

The next route handler, [mailform()](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L48-L54), is the form where you specify email recipients, subject, and body. Note that this function also includes our first call to Graph: [retrieving the user profile](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L51-L51) to get the current user's display name and email address, which are [passed to the mailform.html template](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L52-L54).

Next is the [send_mail()](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L56-L73) function, which sends the email and displays the response from the Graph API. It uses a sendmail() helper function, passing to it the query string parameters that were posted from the form:

```python
response = sendmail(MSGRAPH,
                    subject=flask.request.args['subject'],
                    recipients=flask.request.args['email'].split(';'),
                    html=flask.request.args['body'])
```

The [get_token()](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L75-L78) function is used by the Flask-OAuthlib client instance (```MSGRAPH```) to obtain an access token whenever we make calls to Graph. The access token is passed in an HTTP header named **Authorization**, but you don't need to deal with this in your code. You can simply make calls to Graph via the client's HTTP verb methods (for example, get() or post()), and the client instance will know to call ```get_token()``` to obtain a token, because the function is [decorated](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L75-L75) with ```tokengetter```.

Next comes [request_headers()](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L80-L85), which returns a dictionary of HTTP headers that are sent with each call to Graph.

Finally we have [sendmail()](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L87-L129), the helper function for sending email. It sends a message using the ```me/microsoft.graph.sendMail``` endpoint in the Microsoft Graph API, and you can re-use this function in your own code whenever you need to send email via Microsoft Graph. See the [docstring](https://github.com/microsoftgraph/python-sample-send-mail/blob/master/sample.py#L88-L97) for information about how to call this function.

## Other Python REST samples

In addition to the sample covered above, the following samples demonstrate how to work with other features of Microsoft Graph from Python:

* [Python authentication samples for Microsoft Graph](https://github.com/microsoftgraph/python-sample-auth)
* [Working with paginated Microsoft Graph responses in Python](https://github.com/microsoftgraph/python-sample-pagination)
* [Working with Graph open extensions in Python](https://github.com/microsoftgraph/python-sample-open-extensions)

If there's a particular sample you'd like to see, please let us know by [submitting an issue](https://github.com/microsoftgraph/python-sample-auth/issues). We're very interested in your feedback on any Microsoft Graph scenario you'd like to build in Python!

The Microsoft Graph API is a very powerful, unifiying API that can be used to interact with all kinds of Microsoft data. Check out the [developer documentation](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) or the [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) to explore what else you can accomplish with Microsoft Graph.
