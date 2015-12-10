# Parsing JSON with PHP

The popular format JSON typically represents data as a Javascript literal for interchange. In PHP, the function `json_decode` can be used to convert JSON data to PHP variables. Arrays as well as objects can be converted to equivalent PHP literals.
Here are some examples : 
```php
$json = '["Hello","World"]';                
$array = json_decode($json);
echo($array[0]);    // hello

// objects are converted to PHP objects by default
$jsonObject = '{
                "Name":"John",
                "Surname":"Doe"
                }';
$obj = json_decode($jsonObject);
echo($obj->Surname);   // Doe
```

`json_decode` provides an optional boolean second-argument that if passed as `true` converts objects into PHP associative arrays. Using the example from above, and decoding it as such, the elements can be accessed using the respective keys.
```php
$assocArray = json_decode($jsonObject, true);   // same json object as above
// accessing elements using array syntax
echo($assocArray['Name']);                      // John
```


### Handling Multi-dimensional Objects
`json_decode` can recursively decode JSON strings. The third optional argument tells the function the depth to which decoding should happen. Consider a JSON string that represents an indexed array of objects.
```php
$json = '[
        {"id":"1",
         "name":"foo"
        },
        {"id":"2",
         "name":"bar"
        }
        ]';
$baz = json_decode($json);
echo($baz[0]->name);            // foo
```
If `true` were to be passed as the second argument to `json_decode` above, one would end up with a *numerically indexed* array of *associative arrays*.
```php
// using the same $json as above
$baz = json_decode($json,true);
echo($baz[1]['name']);      //bar
```

#### Iterating over Multidimensional arrays
Consider a JSON string that when decoded results in a multidimensional PHP literal. Such a literal can be efficiently iterated over using `RecursiveIteratorIterator`.
```php
$json = '{
        "John":{
                "gender":"male",
                "age":"12"
                },
        "Jen":{
                "gender":"female",
                "age":"13"
                }
        }';

$iter = new RecursiveIteratorIterator(
    new RecursiveArrayIterator(json_decode($json,true)),
    RecursiveIteratorIterator::SELF_FIRST);
    
foreach($iter as $key=>$value){
    if(is_array($value)){
        echo "$key:\n";
    }else{
        echo "$key => $value\n";
    }
}
```
The output will be :
```
John:
gender => male
age => 12
Jen:
gender => female
age => 13
```

### Errors
`json_decode` returns `NULL` if the JSON string cannot be decoded or the encoded data is deeper than the recursion limit. In such cases, the functions `json_last_error` and `json_last_error_msg` provide some useful information. 
```php
$json = '["1"2]';
$var = json_decode($json);
var_dump($var);                     // NULL
echo json_last_error();             // 4
echo json_last_error_msg();         // Syntax error
```

# References
1. [json_decode](http://php.net/manual/en/function.json-decode.php)
2. [RecursiveIteratorIterator](http://php.net/manual/en/class.recursiveiteratoriterator.php)