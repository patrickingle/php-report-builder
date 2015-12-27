# PHP Report Builder Usage #

## Installation ##

Installing the function is easy.  Simply download it and include it in your project.

## Basic Usage ##

The report\_builder() function takes two arguments, _$template_ and _$data_.

  * _$template_ is the HTML template which provides the layout of the report.
  * _$data_ is a specifically formatted array containing the data with which to populate the template.

Here is an example template:

```
<html>
    <body>
        <p>Today's date is %date%.</p>
        <p>Welcome, %user%!</p>
    </body>
</html>
```

The _$data_ array to populate the report would look something like this:

```
$data = array(
    "main" => array(
        "date" => "February 24, 2012",
        "user" => "Nick")
    );
```

When you call _report\_builder($template, $data)_, the function will return this:

```

<html>
    <body>
        <p>Today's date is February 24, 2012.</p>
        <p>Welcome, Nick!</p>
    </body>
</html>
```

Report Builder also supports lists.  For instance, you can create a single block of code within HTML which will be repeated for each instance of the data in the passed array.  This could be useful for generating something like an invoice where the number of products is unknown at template creation time.  Your _$template_ may look like this (with much better styling via CSS, of course, but I'll leave that to you):

```
<html>
    <body>
        <p>Hyperion Works Invoice</p>
        <p>Date: %date%</p>
        <p>Customer: %customer%</p>

        <table>
            <tr>
                <th>Quantity</th>
                <th>Item</th>
                <th>Unit</th>
                <th>Total</th>
            </tr>
            [items]
            <tr>
                <td>%quantity%</td>
                <td>%item%</td>
                <td>%unit%</td>
                <td>%total%</td>
            </tr>
            [/items]
         </table>
         <p>Total: %total%</p>
    </body>
</html>
```

And the _$data_ array passed to the function to populate this template may look like the following:

```
$data = array(
    "main" => array(
        "date" => "February 24, 2012",
        "customer" => "John Doe",
        "total" => "\$100.00")
    ),
    "lists" => array(
        "items" => array(
            "quantity" => "2",
            "item" => "Some Item",
            "unit" => "\$25.00",
            "total" => "\$50.00"),
        array(
            "quantity" => "5",
            "item" => "Some Other Item",
            "unit" => "\$10.00",
            "total" => "\$50.00")
        )
    );
```

The generated template would be returned as follows:

```
<html>
    <body>
        <p>Hyperion Works Invoice</p>
        <p>Date: February 24, 2012</p>
        <p>Customer: John Doe</p>

        <table>
            <tr>
                <th>Quantity</th>
                <th>Item</th>
                <th>Unit</th>
                <th>Total</th>
            </tr>
            <tr>
                <td>2</td>
                <td>Some Item</td>
                <td>$25.00</td>
                <td>$50.00</td>
            </tr>
            <tr>
                <td>5</td>
                <td>Some Other Item</td>
                <td>$10.00</td>
                <td>$50.00</td>
            </tr>
         </table>
         <p>Total: $100.00</p>
    </body>
</html>
```

The _$data_ array is broken into two components:
  * "main", which holds an array of the one-time replacement variables.
  * "lists", which contains an array of arrays, the outer-most array being related to `[block][/block]` where "block" is the key of this array, and the inner-most array contains the details of the iterations the block.

Within both "main" and "lists", the value of "key" will replace "%key%" in the template.  Keys need only be unique within their scope (i.e. either within "main" or within their own list).