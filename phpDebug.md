# Debugging in PHP

In this article, we discuss various ways of debugging PHP scripts, from printing variable values to using sophisticated debuggers.

1. ### Printing variables

An elementary way to try to debug code is to print the relevant variables and possibly, have the script exit at those points. This can be done in a couple of ways.
* `print_r`
```php
echo '<pre>';
print_r($foo);
print_r($bar);
echo '</pre>';
```
* `var_dump`
```php
var_dump($foo, $bar);
// exit();
```
The output of `print_r` and `var_dump` differs slightly in the formatting and variable information printed. Also, `print_r` doesn't take multiple arguments.   
While this might seem like a trivial way to debug applications, but using it at appropriate points found by analyzing the application structure can be quite useful. 

2. ### Backtrace

The function `debug_print_backtrace` prints the PHP backtrace when called. While it can be put anywhere, it can be particularly useful in an exception handler.
```php
set_exception_handler('exception_handler');

function exception_handler(Exception $e){
    // do things
    // might dump some variables here for added info
    debug_print_backtrace();
    exit();
}
```

3. ### Reporting Errors

PHP generates various error messages according to it's configuration in `php.ini` but these are suppressed by default, since it isn't logical for a user to see them. For debugging purposes however, they can be switched on.
The values of the following variables in `php.ini` need to be set appropriately : 
* `display_errors` to 1
* `error_reporting` to desired level of error reporting (eg: E_ALL & ~E_NOTICE)

On shared hosts, it might not be possible to alter `php.ini`. However, the same changes can be conveyed through the .htaccess file : 
```
php_flag  display_errors        on
php_value error_reporting       2039
```
The value of error_reporting is a bit-wise combination of the various kinds of errors that are possible to report. Check out [this link](http://php.net/manual/en/errorfunc.constants.php) for more info.  
The simplest way to enable reporting however, maybe to add equivalent lines to the running script.
```php
ini_set('display_errors', 1);
error_reporting(-1);    // report all errors
```
A slight modification can be made above to ensure that `display_errors` is only set on local/dev machine.
```php
$testServer = $_SERVER['SERVER_NAME'] = "127.0.0.1" || $_SERVER['SERVER_NAME'] = "localhost";
ini_set('display_errors', $testServer);
```
It needs to be mentioned that the parameter `display_errors` need not be set in order to view errors. If `error_reporting` is set, then the errors are logged in the PHP log, and can be read from there. The `error_log` entry in `php.ini` declares the location of the log file.

4. ### Registering custom Error Handler

An error handler is called whenever an error occurs. The advantage of using an error handler is that no matter what the value of `error_reporting` is, the handler is always called and errors can be logged.
```php
function logErrors($errorNum, $errorStr){
    file_put_contents("/tmp/php_errors.php", date('Y-m-d H:i:s - ') . $errorStr, FILE_APPEND);
}
    set_error_handler('logErrors');
```
    
5. ### Using Debuggers 

A number of tools can be used as editors and/or debuggers with PHP and popular editors like NetBeans/Eclipse. Some of these tools are mentioned below : 
1. [PHPEclipse](http://www.phpeclipse.com/) - syntax highlighting, PHP scripts previews, setting breakpoints
2. [Xdebug](http://xdebug.org/) - Very popular, integrates with Eclipse and NetBeans
3. [PHPEd](http://www.nusphere.com/products/phped.htm) - editor, comes with a debugger
4. [FirePHP](https://addons.mozilla.org/en-us/firefox/addon/firephp/) - Firefox add-on to debug PHP alongside Javascript
5. [PHPStorm](https://www.jetbrains.com/phpstorm/) - code editor
6. [Nette Tracy](https://tracy.nette.org/) - visualisation of errors and exceptions
7. [dBug](http://dbug.ospinto.com/) - lighweight, quick tool
8. [Zend Studio](http://www.zend.com/en/products/studio) - breakpoints, stepping over code, proprietary software

# References
1. [print_r](http://php.net/manual/en/function.print-r.php)
2. [debug_print_backtrace](http://php.net/manual/en/function.debug-print-backtrace.php)
3. [error_reporting](http://php.net/manual/en/function.error-reporting.php)
4. [IBM article on PHP Debugging](http://www.ibm.com/developerworks/library/os-debug/)
5. [set_error_handler](http://php.net/manual/en/function.set-error-handler.php)