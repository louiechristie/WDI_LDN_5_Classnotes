##RUBY HASHES AND SYMBOLS

###Symbols

What is a symbol? A symbol is another type of object in ruby, very similar to a string. We write a symbol like:  
`:name `  
or  
`:"name"`  

Why would we use a symbol rather than a string?   

* A symbol is immutable. This means it cannot be mutated
* A symbol is stored in one place in memory. They are totally unique. This means they are more efficient
* They are more readable

What are the downsides?

* They are immutable, so are less flexible
* They have less methods, so are less versatile

Let's look at this "uniqueness" concept.

Example:  

```
$ str1 = "hello"
$ str1 = "hello"
$ str1.object_id
=> 789321400
$ str1.object_id
=> 789321480
```

You can see that both those objects, while looking absolutely identical, have a different, unique ID. 

```
$ str1 == str2
=> true
$ str1.object_id == str2.object_id
=> false
```

However, symbols have exactly the same ID. Using symbols instead of strings will save up some place in the computer's memory (Random Access Memory) and speed up your code slightly. At the scale of the programs we will be building over the next weeks, though, this added efficiency gain is negligible. 

We can convert a string into a symbol by using:  
`.to_sym `  
or  
`.intern` 


###Hashes

What is a Hash? A hash is a collection of unique key/value pairs, also referred to as "associative arrays". They are similar to arrays, except you choose the key which the value is associated with, rather than it being automatically associated with an index. They are both enumerable.

Normally hashes are not ordered, if we want an ordered list, use an array. Do not rely on your hash remaining ordered!


####Initialization

We initialize a hash with curly braces: ` h={}`

We can create an empty hash using the class "constant".  
`$ h = Hash.new `         
`=> {}`  

To set a default value for when keys are missing:  
`$ h = Hash.new(99)`  
`=> {} `         
`$ h[:some_key]`  
`=> 99`  

When we write a hash, we will make the key a symbol. They can be a different type of object, but using a unique symbol is much simpler as all keys must also be unique.

Example:  
`{key: :value, key: "value", key: 1}`  
Other notation:  
`{:key => value, :key => "value, :key => 1, ...}` (the "fat-arrow" syntax is still commonly used, even if it is starting to die out)

So what is the benefit of using a hash? 

By using a hash we have a unique name for each value we want to store. We can use this key to fetch and set our values. 

Example:

```
$ my_hash = {top: 10, right: 20, bottom: 10, left: 20}
$ my_hash[:top]       RETURNS 10
$ my_hash[:right] = 30  RETURNS   {top: 10, right: 30, bottom: 10, left: 20}
```

If we ask for a missing key, we get 'nil' by default.


####Methods: 

We can return all the keys in our hash by using:  
`.keys`  
And all the values by using:  
`.values`  
T delete an item from our hash using:  
`.delete(:key)`  

To "merge" one `Hash` into another:  

```
$ hash_1 = {:a => 1, :b => 2}
$ hash_2 = {:b => 3, :c => 4}
$ hash_3 = hash_1.merge(hash_2)
=> {:a => 1, :b => 3, :c => 4}
```

You'll notice that the common key `:b` is taken from `hash_2`
â†’ with `.merge()`, the right hash overwrites the left one.



#### Fetch:

By default, using `#[]` will retrieve the hash value if it exists, and return ` nil` if it doesn't exist.

Using `#fetch` gives you a few options:  

* `fetch(key_name)`: get the value if the key exists, raise a KeyError if it doesn't
* `fetch(key_name, default_value)`: get the value if the key exists, return default_value otherwise
* `fetch(key_name) { |key| "default" }`: get the value if the key exists, otherwise run the supplied block and return the value.

Each one should be used as the situation requires, but `#fetch` is very feature-rich and can many cases depending on how it's used.


`#fetch` Returns a value from the hash for the given key. If the key can't be found, there are several options: With no other arguments, it will raise an KeyError exception; if default is given, then that will be returned; if the optional code block is specified, then that will be run and its result returned.

```
h = { "a" => 100, "b" => 200 }
h.fetch("a")                            #=> 100
h.fetch("z", "go fish")                 #=> "go fish"
h.fetch("z") { |el| "go fish, #{el}"}   #=> "go fish, z"
```

The following example shows that an exception is raised if the key is not found and a default value is not supplied.

```
h = { "a" => 100, "b" => 200 }
h.fetch("z")
```

produces:

```
prog.rb:2:in 'fetch': key not found (KeyError)
 from prog.rb:2
```

####Advantages:

There are many reasons to use hashes, as they are: 

* fast: Ruby can create/manipulate hashes very quickly
* convenient: hashes have lots of very useful methods
* flexible: each record can have different attributes

Hashes are a good choice, when simply getting & setting temporary data
As mentioned before, hash values can be any type of object
