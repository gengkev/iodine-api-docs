# Authentication

Authentication is required for most APIs, excluding the Day Schedule API.

To remain logged in, you must handle cookies yourself. You must retain the PHPSESSID cookie, and optionally the IODINE_PASS_VECTOR cookie.

Most of the source code pertaining to authentication can be found in [auth.class.php5](https://github.com/tjcsl/iodine/blob/master/modules/auth/auth.class.php5).



## Authentication error

If you make a request through the standard API that requires authentication, but you aren't logged in, you will receive a `401 Unauthorized`. This could be because you never logged in, or your session expired. You should then attempt to authorize.


#### Example Result

```
401 Unauthorized
```

Body:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<auth>
 <error>
  <message>You are not logged in.</message>
  <login_base_url/>
 </error>
</auth>
```



## Login (POST /api/)

To authenticate, submit a POST request to any URL while logged out. Login successes will always return a status of `302 Found`, redirecting to the originally requested page.

Normally, login failures will return the login page with an error code. However, if the URL begins with `/api/`, the server returns a parseable XML representation of the failure, with a status of `200 OK`. A parameter `id` is returned inside the `auth` and `error` tags, the values of which are summarized below.

- 0: unknown
- 1: bad_password
- 2: not_in_database
- 3: bad_username
- 4: tj_email_suffix
- 5: not_yet_active

The normal user interface displays more detailed explanations when a login failure occurs. To see them, examine the source code of [the login form](https://github.com/tjcsl/iodine/blob/master/templates/login/login.tpl#L65), where the errors are displayed.


#### Parameters

These are sent in the POST body of the request with content type `application/x-www-form-urlencoded`.

- `login_username` (required) The username to log in with (ex. `awilliam`)
- `login_password` (required) The password to log in with (ex. `password`)


#### Example Request

Headers:

```
POST /api/
Content-Type: application/x-www-form-urlencoded
```

Body:

```
login_username=awilliam&login_password=password
```


#### Example Result (success)

It isn't really clear why there is HTML returned when login succeeds, but there is. It can be ignored. Instead you may want to make sure the status code of the response is 302 Found. If so, make sure not to follow redirects.

Headers:

```
302 Found
Content-Type: text/html
Location: [same as request URL]
Set-Cookie: IODINE_PASS_VECTOR=[encrypted password]; path=/; domain=iodine.tjhsst.edu
Set-Cookie: PHPSESSID=[new session id]; path=/; secure
```

Body:

```html
<script type="text/javascript" src="https://iodine.tjhsst.edu/www/js/cookie.js"></script>
<script type="text/javascript" src="https://iodine.tjhsst.edu/www/js/genresize.js"></script>
<script type="text/javascript" src="https://iodine.tjhsst.edu/www/js/ieemu.js"></script>
<script type="text/javascript">
	var default_width = '70%';
</script>
<script type="text/javascript" src="https://iodine.tjhsst.edu/www/js/debug.js"></script>undefined</body>undefined</html>
```


#### Example Result (failure)

Note that the status code for a failure is `200 OK`.

Headers:

```
200 OK
Content-Type: application/xml
```

Body:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<auth>
  <error>
    <success>false</success>
    <loginerror/>
    <id>1</id>
    <message>Login failed.</message>
    <login_base_url>https://iodine.tjhsst.edu/</login_base_url>
  </error>
</auth>
```



## Logout (GET /logout)

This invalidates the session ID cookie on the server.


#### Example Result

```
302 Found
Location: https://iodine.tjhsst.edu/
```
