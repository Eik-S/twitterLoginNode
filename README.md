# Twitter-Login backend in node.js
A simple backend for the twitter sign-in.
[Sign in with Twitter](https://dev.twitter.com/web/sign-in)

I followed twitters instructions:
[Implementing Sign in with Twitter](https://dev.twitter.com/web/sign-in/implementing)

For communication with the front-end, i use the lovely socket.io library:
[Socket.io - git](https://github.com/socketio/socket.io)
And for the front-end javascript:
[Socket.io-client - git](https://github.com/socketio/socket.io-client)

## Motivation:
With the oauth restrictions of Twitters API-1.1 it became quite hard crawling the networks data for mere mortals.
Its necessary to have a certain number of customer or access tokens to stack the number of requests available for your purpose. [Twitter API rate limits](https://dev.twitter.com/rest/public/rate-limits)

My first approach was to create different applications and switch through their credentials while crawling.
This method is hard to scale, because each account creating an application needs a linked phone number and has to be created manually.

Another approach is to use the same consumer credentials (application credentials) with different access credentials (being created if twitter-users sign in through your app.
This method is much more scalable and seems more consistent with Twitters terms of service.
You simply collect more users of your application to be able to send more API requests.

## Usage:
Before using the tool, some stuff needs to be done:

### Add a ./config.js
```javascript
var config = {
    twitterCred: {
        consumerKey: "<Your applications consumer Key>",
        consumerSecret: "<Your applications consumer Secret>",
        callback: "<redirect url if user finished twitter sign-in>",
    },
    dbCred: {
        host: "<mysql host (e.g. localhost)>",
        user: "<mysql user>",
        password: "<mysql password>",
        database: "<database containing userCredentials table>"
    },
    pages: {
        userExistsPage: "<Link to the page, already existing users should be redirected to>",
        errorPage: "<Link to the error 'pls try again' page>"
    }
};
module.exports = config;
```
### Prepare your database
This implementation works with mysql, if you prepare another db for some reason, you simply have to exchange the mysql module and change the **dbReadData()** and **dbWriteData()** functions in [index.js](./index.js)

To use the implemented mysql library, you need to set up a database with the following table:
```
CREATE TABLE userCredentials ( 
    requestToken VARCHAR(100) PRIMARY KEY,
    requestTokenSecret VARCHAR(100),
    accessToken VARCHAR(100) UNIQUE KEY,
    accessTokenSecret VARCHAR(100),
    userName VARCHAR(100)
);
```
Feel free to play around with the string length for the columns.

### Set up the front-end
The folder [frontendTemplates](./frontendTemplates) contains some example html files.
These also contain the javascript for backend connection.
Have a closer look at [index.html](./frontendTemplates/index.html) and [thankyou.html](./frontendTemplates/thankyou.html).
The other files are just for redirecting the user at an error or if she/he is already in the database.

### Start crawling!
Everything should be ready to use!
With the collected credentials, you should soon be able to crawl a bigger amount of twitter data for your projects.

An example crawler, collecting the followers of each twitter user can be find here:
[Twitter crawler](https://github.com/tracemap/tracemap-crawlers/tree/master/twitter)

## Issues
If you use some nonroot webspace for your frontend, make sure your provider allows sockets.
Mine didnt, so i configured an apache on the root server, where this backend lives.
If you experience client-server connection issues, try this approach.

## And finally...
If you like the project, you can help me best with your twitter sign-up at my website.
[twitter.tracemap.info](http://twitter.tracemap.info)

