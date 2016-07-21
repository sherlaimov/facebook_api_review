# Facebook API Review

Before you start using the SDK, you will need a Facebook App ID , which you can create and retrieve on the App Dashboard. Please, refer to this well-documented guide to [register and configure your app](https://developers.facebook.com/docs/apps/register). After you've created the app, come back to go over the key points for working with Facebook API represented below.

Also, before you go on any further, I recommend that you open up and pin the following four pages essential to our work with Facebook API.

1. [Facebook Login for the Web with the JavaScript SDK](https://developers.facebook.com/docs/facebook-login/web) - this one has got a full code exmaple to bootstrap our work with Facbook API.
2. [Graph API Reference](https://developers.facebook.com/docs/graph-api/reference) - this is a full list of Facebook Graph API root nodes - the interface we can use to interact with Facebook.
3. [Graph API Explorer](https://developers.facebook.com/tools/explorer/) - a neat tool provided by Facebook to let you test and fine-tune your queries to their API, aka Graph API.
4. [Facebook SDK for JavaScript - Reference](https://developers.facebook.com/docs/javascript/reference/v2.7) - since we'll be working with the SDK for Javascript, this page is also a good reference point.

## Facebook Login for Your App or Web site
Check out the full code example for Facebook Login below.
``` html
<!DOCTYPE html>
<html>
<head>
<title>Facebook Login JavaScript Example</title>
<meta charset="UTF-8">
</head>
<body>
<script>
  // This is called with the results from from FB.getLoginStatus().
  function statusChangeCallback(response) {
    console.log('statusChangeCallback');
    console.log(response);
    // The response object is returned with a status field that lets the
    // app know the current login status of the person.
    // Full docs on the response object can be found in the documentation
    // for FB.getLoginStatus().
    if (response.status === 'connected') {
      // Logged into your app and Facebook.
      testAPI();
    } else if (response.status === 'not_authorized') {
      // The person is logged into Facebook, but not your app.
      document.getElementById('status').innerHTML = 'Please log ' +
        'into this app.';
    } else {
      // The person is not logged into Facebook, so we're not sure if
      // they are logged into this app or not.
      document.getElementById('status').innerHTML = 'Please log ' +
        'into Facebook.';
    }
  }

  // This function is called when someone finishes with the Login
  // Button.  See the onlogin handler attached to it in the sample
  // code below.
  function checkLoginState() {
    FB.getLoginStatus(function(response) {
      statusChangeCallback(response);
    });
  }

  window.fbAsyncInit = function() {
  FB.init({
    appId      : 'YOUR APP ID GOES HERE',
    cookie     : true,  // enable cookies to allow the server to access 
                        // the session
    xfbml      : true,  // parse social plugins on this page
    version    : 'v2.5' // use graph api version 2.5
  });

  // Now that we've initialized the JavaScript SDK, we call 
  // FB.getLoginStatus().  This function gets the state of the
  // person visiting this page and can return one of three states to
  // the callback you provide.  They can be:
  //
  // 1. Logged into your app ('connected')
  // 2. Logged into Facebook, but not your app ('not_authorized')
  // 3. Not logged into Facebook and can't tell if they are logged into
  //    your app or not.
  //
  // These three cases are handled in the callback function.

  FB.getLoginStatus(function(response) {
    statusChangeCallback(response);
  });

  };

  // Load the SDK asynchronously
  (function(d, s, id) {
    var js, fjs = d.getElementsByTagName(s)[0];
    if (d.getElementById(id)) return;
    js = d.createElement(s); js.id = id;
    js.src = "//connect.facebook.net/en_US/sdk.js";
    fjs.parentNode.insertBefore(js, fjs);
  }(document, 'script', 'facebook-jssdk'));

  // Here we run a very simple test of the Graph API after login is
  // successful.  See statusChangeCallback() for when this call is made.
  function testAPI() {
    console.log('Welcome!  Fetching your information.... ');
    FB.api('/me', function(response) {
      console.log('Successful login for: ' + response.name);
      document.getElementById('status').innerHTML =
        'Thanks for logging in, ' + response.name + '!';
    });
  }
</script>

<!--
  Below we include the Login Button social plugin. This button uses
  the JavaScript SDK to present a graphical Login button that triggers
  the FB.login() function when clicked.
-->

<fb:login-button scope="public_profile,email" onlogin="checkLoginState();">
</fb:login-button>

<div id="status">
</div>
  <div class="status"></div>
</body>
</html>
```
### 1. Checking Login Status
The first step when loading your web page is figuring out if a person is already logged into your app with Facebook login. You start that process with a call to `FB.getLoginStatus`. That function will trigger a call to Facebook to get the login status and call your callback function with the results.

Taken from the sample code above, here's some of the code that's run during page load to check a person's login status:
```javascript
FB.getLoginStatus(function(response) {
    statusChangeCallback(response);
});
```
The response `object` that's provided to your callback contains a number of fields:
``` javascript
{
    status: 'connected',
    authResponse: {
        accessToken: '...',
        expiresIn:'...',
        signedRequest:'...',
        userID:'...'
    }
}
```
`status` specifies the login status of the person using the app. The status can be one of the following:

* `connected`. The person is logged into Facebook, and has logged into your app.
* `not_authorized`. The person is logged into Facebook, but has not logged into your app.
* `unknown`. The person is not logged into Facebook, so you don't know if they've logged into your app. Or FB.logout() was called before and therefore, it cannot connect to Facebook.
* `authResponse` is included if the status is connected and is made up of the following:
* `accessToken`. Contains an access token for the person using the app.
* `expiresIn`. Indicates the UNIX time when the token expires and needs to be renewed.
* `signedRequest`. A signed parameter that contains information about the person using the app.
* `userID` is the ID of the person using the app.

### 2. Logging People In

If people using your app aren't logged into your app or not logged into Facebook, you can use the Login dialog to prompt them to do both. Various versions of the dialog are shown below.

If they aren't logged into Facebook, they'll first be prompted to log in and then move on to logging in to your app. The JavaScript SDK automatically detects this, so you don't need to do anything extra to enable this behavior.

There are two ways to log someone in:

* Use the [Login Button](https://developers.facebook.com/docs/facebook-login/web/login-button).
* Use `FB.login()` from the JavaScript SDK.

### 3. Using the Login Button
If someone hasn't logged into your app yet, they'll see this button, and clicking it will popup a Login dialog, starting the login flow. People who have already logged in won't see any button, or you can also choose to show a logout button to them.
Please, use [this page](https://developers.facebook.com/docs/facebook-login/web/login-button) to generate code for your login button.
Beloq is a login button code snippet which uses the `onlogin attribute to set up a callback that checks the login status:

``` javascript
<fb:login-button scope="public_profile,email" onlogin="checkLoginState();">
</fb:login-button>
```
And this is the callback that calls `FB.getLoginStatus()` to get the current login state:
``` javascript
function checkLoginState() {
  FB.getLoginStatus(function(response) {
    statusChangeCallback(response);
  });
}
```
### 4. Invoking the Login Dialog with the JavaScript SDK
You can invoke the Login Dialog with a simple call to `FB.login()`. There is an optional `scope` parameter that can be passed along with the function call that is a comma separated list of [permissions](https://developers.facebook.com/docs/facebook-login/permissions) to request from the person using the app.  In this case, it would ask for a person's email address and a list of friends who also use the app:
``` javascript
 FB.login(function(response) {
   // handle the response
 }, {scope: 'public_profile,email'});
```
### 5. Handling Login Dialog Response
At this point in the login flow, your app displays the Login dialog, which gives people the choice of whether to cancel or to enable the app to access their data.

Whatever choice people make, the browser returns to the app and response data indicating whether they're connected or cancelled is sent to your app. When your app uses the JavaScript SDK, it returns an `authResponse` object to the callback specified when you made the `FB.login()` call:

This response can be detected and handled within the `FB.login` call, like this:
``` javascript
FB.login(function(response) {
  if (response.status === 'connected') {
    // Logged into your app and Facebook.
  } else if (response.status === 'not_authorized') {
    // The person is logged into Facebook, but not your app.
  } else {
    // The person is not logged into Facebook, so we're not sure if
    // they are logged into this app or not.
  }
});
```
Please, refer to this page to find more info on [Login Dialog](https://developers.facebook.com/docs/reference/javascript/FB.login/v2.7)
##### Facebook Login Best Practices
https://developers.facebook.com/docs/facebook-login/best-practices

## The Graph API
The Graph API is named after the idea of a 'social graph' - a representation of the information on Facebook composed of:
* **nodes** - basically "things" such as a User, a Photo, a Page, a Comment
* **edges** - the connections between those "things", such as a Page's Photos, or a Photo's Comments
* **fields** - info about those "things", such as a person's birthday, or the name of a Page

It's a low-level HTTP-based API that is used to query data, post new stories, upload photos and a variety of other tasks that an app might need to do.

### 1. Reading
All nodes and edges in the Graph API can be read simply with an HTTP `GET` request to the relevant endpoint. 
Most API calls must be signed with an [access token](https://developers.facebook.com/docs/facebook-login/access-tokens/). You can determine which [permissions](https://developers.facebook.com/docs/facebook-login/permissions/) are needed in this access token by looking at the Graph API reference for the node or edge that you wish to read. You can also use the [Graph API Explorer](https://developers.facebook.com/tools/explorer/) to quickly generate tokens in order to play about with the API and discover how it works.

The `/me` node is a special endpoint that translates to the `user_id` of the person (or the `page_id` of the Facebook Page) whose access token is currently being used to make the API calls. If you had a user access token, you could retrieve all of a user's photos by using:
```sh
GET graph.facebook.com
  /me/photos
```
#### Choosing Fields
You can choose the fields or edges that you want returned with the `fields` query parameter. This is really useful for making your API calls more efficient and fast.
For example, the following Graph API call `https://graph.facebook.com/bgolub?fields=id,name,picture` will only return the id, name, and picture in Ben's profile:
```sh
GET graph.facebook.com
  /bgolub?
    fields=id,name,picture
```
#### Ordering
You can order certain data sets chronologically. `order` must be one of the following values:
* `chronological`
* `reverse_chronological`

For example you may sort a photo's comments in reverse chronological order using the key `reverse_chronological`:
```sh
GET graph.facebook.com
  /{photo-id}?
    fields=comments.order(reverse_chronological)
```

| Name        | Description           | Type  |
| ------------- |:-------------:| -----:|
| `return_ssl_resources` | Used when you require a picture to be returned over a secure connection (HTTPS) to avoid mixed content warnings in browsers. | `bool` |
| `locale`      | Used if your app needs the ability to retrieve localized content in the language of a particular locale (when available).     | `Locale` |

#### Making Nested Requests
effectively nest multiple graph queries into a single call. in a single call, you can ask for the first N photos of the first K albums. The response maintains the data hierarchy so developers do not have to weave the data together on the client. There is no limitation to the amount of nesting of levels that can occur here. You can also use a `.limit(n)` argument on each field or edge to restrict how many objects you want to get.
```sh
GET graph.facebook.com
  /me?
    fields=albums.limit(5),posts.limit(5)
```
We can then extend this a bit more and for each album object, also retrieve the first two photos, and people tagged in each photo:
```sh
GET graph.facebook.com
  /me?
    fields=albums.limit(5){name, photos.limit(2)},posts.limit(5)
```

For information regarding **pagination** please refer to this [link](https://developers.facebook.com/docs/graph-api/using-graph-api/#reading)
#### Publishing
Most nodes in the Graph API have edges that can be publishing targets, such as `/{user-id}/feed or /{album-id}/photos`. All Graph API publishing is done with an HTTP `POST` request to the relevant edge with any necessary parameters included. For example, to publish a post on behalf of someone, you would make an HTTP `POST` request as below:
```sh
POST graph.facebook.com
  /{user-id}/feed?
    message={message}&
    access_token={access-token}
```
All publishing calls must be signed with an [access token](https://developers.facebook.com/docs/facebook-login/access-tokens/). You can determine which [permissions](https://developers.facebook.com/docs/facebook-login/permissions/) are needed in this access token by looking at the [Graph API reference](https://developers.facebook.com/docs/graph-api/reference) for the node to which you wish to publish.
#### Updating
All Graph API updating is done with an HTTP `POST` request to the relevant node with any updated parameters included:
```sh
POST graph.facebook.com
  /{node-id}?
    {updated-field}={new-value}&
    access_token={access-token}
```
All update calls must be signed with an access token with the same permissions needed for publishing to that endpoint, as per the Graph API reference for the node that you wish to update.
#### Deleting
Delete nodes from the graph by sending HTTP `DELETE` requests to them:
```sh
DELETE graph.facebook.com
  /{node-id}?
    access_token=...
```
Generally speaking, an **app can only delete content it created**. Check the reference guide for the node or edge for more information.
#### Searching
You can search over many public objects in the social graph with the `/search` endpoint. The syntax for search is:
```sh
GET graph.facebook.com
  /search?
    q={your-query}&
    type={object-type}
```
Here's a list of the most common search types:
* `user`
* `page`
* `event`
* `group`
* `place`

Example:
```sh
GET graph.facebook.com
  /search?
    q=coffee&
    type=place&
    center=37.76,-122.427&
    distance=1000
```
For a more detailed info on the search type please refer to [this page](https://developers.facebook.com/docs/graph-api/using-graph-api#search)
#### Handling Errors
Requests made to our APIs can result in a number of different error responses.  The following represents a common error response resulting from a failed API request:
```javascript
{
	"error": {
		"message": "Message describing the error", 
		"type": "OAuthException", 
		"code": 190,
		"error_subcode": 460,
		"error_user_title": "A title",
		"error_user_msg": "A message",
		"fbtrace_id": "EJplcsCHuLu"
	}
}
```
For a more detailed info on the search type please refer to [this page](https://developers.facebook.com/docs/graph-api/using-graph-api#errors)
