# Delete Array elements by Value

We consider an array `foo` with multiple values, and consider the problem of deleting an element from the array, given *just the value*. Let the variable `$delValue` hold the value to be deleted. Some solutions to the problem can be devised as follows.

1. ### Using `array_search` and `unset`

```php
$key = array_search($delValue, $foo);
if($key !== false)
    unset($foo[$key]);
```
The strict operator `!==` is required because the `array_search` function returns the key of the value in the array, if present. Since the key can be 0 and 0 evaluates to false in PHP, a strict comparison is required.  
The method doesn't work for multiple entries of the value, but with a slight modification, it can be made to : 
```php
while($key = array_search($delValue, $foo) !== false){
    unset($foo[$key])
}
```

2. ### Using `array_diff`

This method only works for arrays whose members can be converted to strings.
```php
$foo = array_diff($foo, [$delValue]);
```
`delValue` is cast into an array, and `array_diff` returns only the values of `foo` that will not be found in `[$delValue]`. Thus, all instances of `delValue` are deleted. This is a concise, powerful way that successfully deletes multiple entries of `delValue`.

3. ### Using `array_keys`

This is similar to the first method, but multiple entries can be successfully deleted.
```php
foreach(array_keys($foo, $delValue, true) as $key){
    unset($foo[$key]);
}
```
The argument `$delValue` tells the function `array_keys` to return keys only for the values *equal to* `delValue`, and the third argument `true` signifies that strict comparison (`===`) be used for checking the values.

4. ### Using Anonymous Functions with `array_filter`

A neat method that only works for versions of PHP >= 5.3.0.
```php
$foo = array_filter($foo, function($value) use ($delValue) {
    return ($value !== $delValue);
});
```
The anonymous function returns a boolean value, which determines whether an element should pass through the filter or not. All values of the array `foo` *strictly not equal to* `delValue` pass through and become a part of the new array. Worth noting that multiple instances of `delValue` are deleted.

5. ### Using `array_flip`

The function flips keys and values of the array passed as an argument. In the case of multiple values, only the latest key is used; the rest are discarded. Also, it's necessary that the values be either integers or strings or be convertible as such.
```php
$flippedFoo = array_flip($foo);
unset($foo[$delValue]);
$foo = array_flip($flippedFoo);
```
Multiple values are deleted as a side-effect of the `array_flip` call, although some data loss can possibly occur.

6. ### Using comparisons and loops

```php
foreach($foo as $key => $value){
    if($value == $delValue){        // === for strict comparison may be used
            unset($foo[$key]);
    }
}
```
Since each element is looped over, multiple values if present are all deleted.

7. ### Using `array_splice`

The function array_splice can also be used to delete values from an array.
```php
$key = array_search($delValue, $foo);
array_splice($foo, $key, 1);
```
The method only deletes one instance of the value from the array. In the case of associative arrays or arrays in which keys have gaps, the method fails.

# References
1. [array_search](http://php.net/manual/en/function.array-search.php)
2. [unset](http://php.net/manual/en/function.unset.php)
3. [array_diff](http://php.net/manual/en/function.array-diff.php)
4. [array_keys](http://php.net/manual/en/function.array-keys.php)
5. [array_flip](http://php.net/manual/en/function.array-flip.php)
6. [array_filter](http://php.net/manual/en/function.array-filter.php)
7. [Anonymous Functions](http://php.net/manual/en/functions.anonymous.php)
8. [array_splice](http://php.net/manual/en/function.array-splice.php)