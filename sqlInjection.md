# Preventing SQL Injection in PHP

Do you remember this great comic ?  

![foo](https://imgs.xkcd.com/comics/exploits_of_a_mom.png "Her daughter is named Help I'm trapped in a driver's license factory")

Let's break this down and see the problem. Consider the following form of a MySQL query in PHP (using mysqli) : 
```php
mysqli_query("INSERT INTO Students VALUES ('" + FNMName.Text + "', " + LName.Text + ")";
```
Substituting the above value of name for FNMName.Text and an imaginary surname for LName.Text results in not one, but two queries : 
```mysql
INSERT INTO Students VALUES ('Robert'); DROP TABLE Students;--', 'FooBar')
```
And there goes the table. This technique of attacking data-driven applications by injection malicious SQL code is known as SQL Injection. Well-written applications that employ proper checks can easily defend themselves against this method. Here are some ways in which apps written in PHP can check SQL Injection.

### Prepared Statements and Parameterized Queries
Let's discuss this method using the two common interfaces that PHP provides for database connections : PDO and MySQLi.
1. PDO  
```php
$stmt = $pdo->prepare('SELECT * FROM Students WHERE name = :name');
$stmt->execute(array('name' => $name));

foreach ($stmt as $row) {
    // do something with $row
}
```
2. MySQLi (for MySQL)
```php
$stmt = $databaseCnn->prepare('SELECT * FROM Students WHERE name = ?');
$stmt->bind_param('s', $name);
$stmt->execute();
$result = $stmt->get_result();

while ($row = $result->fetch_assoc()) {
    // do something with $row
}
```

While using PDO, the database connection must be set up correctly so that PDO knows that **real** parameterized queries have to be used, and not **emulated ones**. Something like this: 
```php
$databaseCnn = new PDO('mysql:dbname=test;host=127.0.0.1;charset=utf8', 'uname', 'passwd');

$dbConnection->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
```

What happens with prepared statements is that the database server parses and compiles them before executing. The **?** and the **:name** parameters tell the server where the query should be filtered. When the query is **executed**, the server combines the query with the search params. The method prevents **Injection attacks** by treating the query separately (as a query and not as a string) from the params which are treated like strings (or numbers, for optimization purposes). Hence, passing a name above like "Robert'); DROP TABLE Students;--" will just result in **one** query instead of **two** which searches for a student with a rather weird name.
```mysql
SELECT * FROM Students WHERE name = "Robert'); DROP TABLE Students;--";
```
An additional benefit of using parameterized queries is that when executing multiple queries of the same kind, minor speed gains can be attained because the server compiles and parses the query once only.

### Escaping Literals
Escaping the special characters in user-entered parameters is another technique to prevent Injection attacks. In comparison to prepared statements, this method requires much fewer changes.
```php
// setup connection
$unsafe_variable = $_POST["user-input"];
$safe_variable = mysql_real_escape_string($unsafe_variable);

mysql_query("Select * FROM Students (name) VALUES ('" . $safe_variable . "')");
```
The [mysql_real_escape_string](http://php.net/manual/en/function.mysql-real-escape-string.php) function call its MySQL equivalent function **mysql_real_escape_string** which prepends backslashes to certain special characters.  
Note : mysql_real_esape_string is deprecated. Please refer the PHP documentation page for alternatives.

##### Possible attack
Consider : 
```php
$query = "SELECT * FROM Students WHERE id = " . $safe_variable;
```
Since the value expected for id is an integer, it is not surrounded by quotes. But, and Injection here is still possible because a parameter of the following kind will pass through the escape check :  
`1 UNION SELECT password FROM users`
In such cases, it'll be essential to conduct checks validating that the param contains only digits.

### Whitelisting
**Whitelisting** deals with dynamic queries, i.e., queries whose structure changes with user input. Consider the following example in which the user generates a dynamic query by deciding how the results should be ordered : 
```php
$orders  = array("name","price","qty"); //field names
$key     = array_search($_GET['sort'],$orders)); // see if we have such a name; 
$orderby = $orders[$key]; // FALSE evals to 0
$query   = "SELECT * FROM `table` ORDER BY $orderby"; 
```

The above two methods can also be used to protect identifiers. They can be **escaped** to prevent attacks or **placeholders** can be devised for identifiers in the same manner as placeholders were put in place for column values.

### Hacks based on Input Types
##### Integer Input
This basically implies tha the programmer makes sure that the user-entered input really is an integer. Here's a neat way to do it : 
```php
$query = sprintf("SELECT 1,2,3 FROM table WHERE 4 = %u", $input);
```

#### Other than Integer
