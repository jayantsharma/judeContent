# PHP : Passing Variables between Pages

Let's assume a PHP variable `foo` on page1.php.There is a form on this page which posts on page2.php. Since HTTP is stateless, webpages are disconnected and this implies that page2.php won't be aware of the existence of page1.php or any date thereof. page2 can still access the variables from the page1 script using the methods discussed here.

* ### Sessions

Sessions store data on the server for access between php scripts on different web-pages.
```php
// on page1
session_start();
$_SESSION['foo'] = $foo;

// on page2
session_start();
$foo = $_SESSION['foo'];
```
It's important to note that the `session_start` line must be used prior to using the `$_SESSION` superglobal.

* ### Cookies

Cookies differ from Sessions in that they store the date on the client machine instead of the server, and are considered relatively un-safe for this reason. 
```php
// on page1
setcookie('foo', $foo);

// on page2
$foo = $_COOKIE['foo'];
```
Cookies maybe preferred over sessions in situations where date needs to persist, but it's also possible to do the same by storing the data in a database and retrieving using an id or username.

* ### GET and POST params

The variable can either be added as a *GET* parameter in the link to page2 : 
```
<a href="page2.php?foo=<?php echo urlencode($foo);?>">Go to Page 2</a>
```
Or, a hidden field can be created in a form that posts to page2 : 
```
<form method="post" action="page2.php">
    <input type="hidden" name="foo" value=<?php echo $foo;?>>
    <input type="submit"
</form>
```
Then, on page2 the variables can be retrieved as : 
```php
// using GET method
$foo = $_GET['foo'];
// using POST method
$foo = $_POST['foo'];
// using GET, POST or COOKIE
$foo = $_REQUEST['foo'];
```
GET and POST, both methods are considered insecure because the it's possible to fiddle with this data before sending it to the server.

# References
1. [session_start](http://php.net/manual/en/function.session-start.php)
2. [$_SESSION](http://php.net/manual/en/reserved.variables.session.php)
3. [setcookie](http://php.net/manual/en/function.setcookie.php)
4. [$_COOKIE](http://php.net/manual/en/reserved.variables.cookies.php)
5. [$_GET](http://php.net/manual/en/reserved.variables.get.php)
6. [HTML Forms](http://www.w3schools.com/html/html_forms.asp)