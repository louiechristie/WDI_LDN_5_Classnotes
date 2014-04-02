##RUBY ARRAYS

An array is an ordered list in Ruby. They are generally indexed from left to right, and will start at 0. We identify an array from the `[]` square brackets. Arrays in ruby can hold different types of objects, as long as every object is comma separated.  
`['obj1', 1, 'obj3']`

We can also write an array as:  
`%w(obj1 obj2 obj3 obj4)`

We can have any object in our array, including other arrays:  

```
$ my_array = [[1,2,3,4],[4,5,6,7],[7,8,9,10]]
$ my_array.length       RETURNS   3
$ my_array[0].length    RETURNS   4
$ my_array[1][1]      RETURNS   5
```

###Array methods

To find all the methods which are available to us when using arrays use the command:  
`.methods`

To find the length of our array we use:  
`.length`  
or  
`.size`  

To find out if we have any values in our array we use:  
`.any?`  

To get the first value â†’ `.first`  
To get the last value â†’ `.last`  

To get a specific index (remember it starts at 0) we use `[1]`
Example:  
`$ my_array = [1,2,3,4,5]`
`$ my_array[0]`   RETURNS     `1`

To reverse lookup our array we use `[-1]`  
`$ my_array = [1,2,3,4,5]`  
`$ my_array[-1] ` RETURNS     `5`  
  
To flatten our arrays we use `.flatten` or  `.flatten! `
Example:  
`$ my_array = [[1,2,3,4],[4,5,6,7],[7,8,9,10]]`  
`$ my_array.flatten`    RETURNS   `[1,2,3,4,4,5,6,7,7,8,9,10]`  

To remove an element off the end of our array we use:  
`.pop`  
To remove an element from the beginning of our array use:  
`.shift`  
To add an element back on to the front of the array use:  
`.unshift(x)`  

To add an element back on to the end of our array, we use `.push(x)` or you may also see `<< x`

To insert an element at a specific index we use `.insert 2 "object"`, "2" being the index.
 
To remove an element at located a specific index we use `.delete_at(2)`, "2" being the index. 

We can turn a string into an array by using  `.split(x)`  
The variable we enter to our split method will tell Ruby at which point in the string you want to split on.  
Example;  
`$ my_array = "hello there"`
`$ my_array.split(" ")  `   RETURNS   `["hello", "there"]`

To turn our array back into a string, we use `.join(x)`
Again the variable we pass is the character that we want to join on. 
Alternatively we can use `.to_s`

To remove nil elements from our array we have the method:  
`.compact `  
or  
`.compact!`  

We can find elements in our array by using `.include?`
Example:  
`$ my_array = ["hi", "everyone"]`
`$ my_array.include?("hi") `    RETURNS `true`

Other methods which return a boolean value are:  
`.none?`  
`.any?`  
`.empty?`  

To find the highest value in our array we use: (this also works for alphabetical characters.):  
`.max`  
To find the smallest :  
`.min` 

To sort our array  we use  `.sort`  
Then to reverse this we use  `.reverse`  

We can add two arrays together using the `+`  
`[1,2,3]+[3,4,5]`     RETURNS   `[1,2,3,3,4,5]`

To remove any duplicate elements we can use `_.uniq`  

We can use intersection on our arrays by using the `&`  
`[1,2,3] & [3,4,5]  ` RETURNS   `[3]` 

###ADDITIONAL RESOURCES

Ruby Arrays:  
<http://ruby-doc.org/core-2.0.0/Array.html>  

