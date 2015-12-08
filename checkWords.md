## PHP : Checking for Words in a String  
  
Consider the following code fragment : 
```php
$str = "Hello World";

if($str contains "Hello")
    echo 'true';
```
 
What would be an efficient code replacement for `$str contains "Hello"` in PHP ?

1. # Using `strpos`  

The PHP function `strpos` returns the *first* position in the `$haystack` string at which the `$needle` string occurs. Here's how it can be used in this context :
```php
if(strpos($str, "Hello") !== false)
    echo "true";
```  
 Note the use of the equality operator `!==`. `strpos` returns the numeric position of occurrence of the `$needle` string *if it occurs*. and `false` otherwise. Since string positions begin at 0 and 0 evaluates to `false` in PHP, strict equality is ensured using the `!==` operator.  
For case-insensitive comparisons, the related function `stripos` can be used.

2. # Using Regular Expressions  

PHP offers functions to enable pattern matching using regular expressions. Using the `preg_match` function : 
```php
if(preg_match("/Hello/", $str)
    echo "true";
```
`preg_match` performs a regex search in the given `$string` and returns 1 if the string matches, 0 if not, and `false` if an error occurs. It is to be noted though, that `preg_match` is slower than `strpos` and will give reduced performance on large number of search operations.  
The above solution is plagued by the problem that it matches the substring, and not the *word*. Therefore, the above match will be successful for the following strings :
* "Hellozzzz"
* "HiHelloHowDoYouDo"  

To identify clear words instead of strings, the pattern needs to be refined : 
```php
$word = "Hello";
if(preg_match('/\b' . preg_quote($word) . '\b/', $string))
    echo "true";
```
The character `\b` signifies word boundaries. A word boundary is identified by the presence of a character that is not alphanumeric or an underscore. Therefore, some cases can still fail:
* "Erm_Hello"
* "Hello2u"  

3. # Using `strstr`

An indirect check to confirm the presence of the word : 
```php
if(strstr($string, 'Hello') !== false)
    echo 'true';
```
For case-insensitive searches, `stristr` can be used. The above method is weaker since it searches for the substring, disregarding word boundaries.

4. # Using `substr_count`

A function which avoids any boolean problems : 
```php
if(substr_count($string, 'Hello') > 0)
    echo 'true';
```
Again, there isn't any regard for word boundaries, and the function basically checks for substrings. While neater, it's slower than `strpos`.

5. # Manipulating strings using `explode`

The string to be searched is exploded on common word boundary characters, and the resultant arrays are then searched for membership of the *word*.
```php
$word = 'Hello';
$spaceWords = explode(' ', $string);
$nonBreakingSpaceWords = explode(chr(160), $string);    // &nbsp
if(in_array($word, $spaceWords) || 
        in_array($word, $nonBreakingSpaceWords))
                echo 'true';
```

6. # Using `mb_strpos`

The function `mb_strpos` is similar to the `strpos` function but makes sure that the search is multi-byte safe. The code remains exactly the same : 
```php
if(mb_strpos($string, 'Hello') !== false)
    echo 'true';
```
If one can't be sure that the incoming data is in *UTF-8*, `mb_strpos` or `mb_stripos` for case-insensitive search can be used.

# References
1. [strpos](http://php.net/manual/en/function.strpos.php)
2. [preg_match](http://php.net/manual/en/function.preg-match.php)
3. [preg_quote](http://php.net/manual/en/function.preg-quote.php)
4. [strstr](http://php.net/manual/en/function.strstr.php)
5. [substr_count](http://php.net/manual/en/function.substr-count.php)
6. [explode](http://php.net/manual/en/function.explode.php)
7. [mb_strpos](http://php.net/manual/en/function.mb-strpos.php)
8. [Understanding Unicode and Character Sets](http://www.joelonsoftware.com/articles/Unicode.html)