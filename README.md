Isomorphic Cookies
========
Isomorphic boilerplate cookie functions for client and server

 - __100% Tests coverage__
 - Works on both server and client

Install:
========
```shell
meteor add ostrio:cookies
```

API:
========
__Note:__ On server, cookies will be set __only__ after headers is sent (on next route or page reload). To send cookies from client to server without page reload use `send()` method

__Server Usage Note:__ On server Cookies implemented in middleware. To get access to current user's cookies use `req.Cookies` instance. For more - see examples section below.

#### Fetch cookies `new Cookies(opts)` [*Isomorphic*]
  Create new instance of Cookies
  - `opts.auto` {*Boolean*} - [*Server*] Auto-bind in middleware as `req.Cookies`, by default `true`
  - `opts.handler` {*Function*} - [*Server*] Middleware function with one argument `cookies` as `Cookies` instance. See "Alternate Usage" section
  - `opts.TTL`  {*Number*} - Default cookies expiration time (max-age) in milliseconds, by default - 31 day
  - `opts.runOnServer` {*Boolean*} - Set to `false`to avoid server usage (by default - `true`)
```javascript
var cookies = new Cookies();
```

#### `cookies.get(key)` [*Isomorphic*]
  Read a cookie. If the cookie doesn't exist a `null` will be returned.
  - `key` {*String*} - The name of the cookie to read

#### `cookies.set((key, value, [opts])` [*Isomorphic*]
  Create/overwrite a cookie.
  - `key` {*String*} - The name of the cookie to create/overwrite
  - `value` {*String*} - The value of the cookie
  - `opts.expires` {*Number*|*Date*|*Infinity*}  - [Optional] The max-age in seconds (e.g. 31536e3 for a year, Infinity for a never-expires cookie), or the expires date in GMTString format or as Date object; if not specified the cookie will expire at the end of session (number – finite or Infinity – string, Date object or null)
  - `opts.path` {*String*} - [Optional] The path from where the cookie will be readable. E.g., "/", "/mydir"; if not specified, defaults to the current path of the current document location (string or null). The path must be absolute (see RFC 2965). For more information on how to use relative paths in this argument, see: https://developer.mozilla.org/en-US/docs/Web/API/document.cookie#Using_relative_URLs_in_the_path_parameter
  - `opts.domain` {*String*} - [Optional] The domain from where the cookie will be readable. E.g., "example.com", ".example.com" (includes all subdomains) or "subdomain.example.com"; if not specified, defaults to the host portion of the current document location (string or null)
  - `opts.secure` {*Boolean*} - [Optional] The cookie will be transmitted only over secure protocol as https (boolean or null)

#### `cookies.remove([key], [path], [domain])` [*Isomorphic*]
 - `remove()` - Remove all cookies on current domain
 - `remove(key)` - Remove a cookie on current domain
 - `remove(key, path, domain)`:
    - `key` {*String*} - The name of the cookie to create/overwrite
    - `path` {*String*} - [Optional] The path from where the cookie was readable. E.g., "/", "/mydir"; if not specified, defaults to the current path of the current document location (string or null). The path must be absolute (see RFC 2965). For more information on how to use relative paths in this argument, see: https://developer.mozilla.org/en-US/docs/Web/API/document.cookie#Using_relative_URLs_in_the_path_parameter
    - `domain` {*String*} - [Optional] The domain from where the cookie was readable. E.g., "example.com", ".example.com" (includes all subdomains) or "subdomain.example.com"; if not specified, defaults to the host portion of the current document location (string or null)

#### `cookies.has(key)` [*Isomorphic*]
 Check whether a cookie exists in the current position, returns boolean value
 - `key` {*String*} - The name of the cookie to check

#### `cookies.keys()` [*Isomorphic*]
  Returns an array of all readable cookies from this location

#### `cookies.send()` [*Client*]
  Send all current cookies to server



Examples:
=========
```javascript
/* Both Client & Server */
var cookies = new Cookies()

/* Client */
if(Meteor.isClient){
  cookies.set('locale', 'en'); //true
  cookies.set('country', 'usa'); //true
  cookies.set('gender', 'male'); //true

  cookies.get('gender'); //male

  cookies.has('locale'); //true
  cookies.has('city'); //false

  cookies.keys(); //['locale', 'country', 'gender']

  cookies.remove('locale'); //true
  cookies.get('locale'); //null

  cookies.keys(); //['country', 'gender']

  cookies.remove(); //true
  cookies.keys(); //[""]

  cookies.remove(); //false
}

/* Server */
if(Meteor.isServer){
  WebApp.connectHandlers.use(function(req, res, next){
    cookies = req.Cookies;

    cookies.set('locale', 'en'); //true
    cookies.set('country', 'usa'); //true
    cookies.set('gender', 'male'); //true

    cookies.get('gender'); //male

    cookies.has('locale'); //true
    cookies.has('city'); //false

    cookies.keys(); //['locale', 'country', 'gender']

    cookies.remove('locale'); //true
    cookies.get('locale'); //null

    cookies.keys(); //['country', 'gender']

    cookies.remove(); //true
    cookies.keys(); //[""]

    cookies.remove(); //false
  });
}
```

### Alternate Usage
```javascript
/* Client */
if(Meteor.isClient){
  var cookies = new Cookies()
  cookies.set('gender', 'male'); //true
  cookies.get('gender'); //male
  cookies.has('city'); //false
  cookies.keys(); //['gender']
}

/* Server */
if(Meteor.isServer){
  var cookie = new Cookies({
    auto: false, // Do not bind middleware by default
    handler: function(cookies){
      cookies.set('gender', 'male'); //true
      cookies.get('gender'); //male
      cookies.has('city'); //false
      cookies.keys(); //['gender']
    }
  });

  WebApp.connectHandlers.use(cookie.middleware());
}
```