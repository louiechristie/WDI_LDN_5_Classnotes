##RUBY BLOCKS AND ENUMERABLES


###Blocks

A block is a section of code which can be passed to a method. They are very similar to methods, but are unnamed. We can write blocks like:  
`$ 10.times { puts "hi" } `  
or

```
$ 10.times do
$   puts "hi"
$ end
```

###Using Blocks for enumeration

Enumerators become useful when you work with arrays and/or hashes and want to list values inside theses hashes/arrays.

We will often use a block to enumerate through our enumerable objects, such as hashes and arrays. 

####Each:

Examples:

```
my_array = [1,2,3,4,5]
my_array.each {|num| num + 1}   RETURNS   [2,3,4,5,6] 

$ my_array = [2,4,6,8,10]
$ my_array.each do |value|
$   value + 10
$   a = []
$ end
=> [12,14,16,18,20]
```

The value within the `||` brackets is the current value from the array which is being passed to the block on each iteration. `num` is a variable, it can be changed to whatever value you like. `num` is also local only to the block, which means that you cannot access it outside the block.

We can do anything with the code inside our block. For example we can use an if statement.  
Example:  
`(1..10).each{ |i| a << i if i % 7  == 0 }`

We can also return values according to the output of the block  

```
$ ["Fred", "Wilma", "Barney" ].each_with_index do |name, index|
$    puts "#{name} is #{index.odd? ? :female : :male} name"
$ end
```

####Select:

Alternatively we can use `.select` to return an array of values which equate to true once passed through our block.

Examples:

```
(1..10).select { |i| i if i % 7 == 0 } 

(1..10).find_all { |i|  i % 3 == 0 }  RETURNS [3, 6, 9]

[1,2,3,4,5].select { |num|  num.even?  }  RETURNS [2, 4]
```

####Detect:

We also have a `.detect` method which finds the first thing in the enumerable which matches the block:  
`(1..10).detect { |i| i % 7 == 0} RETURNS 7`

####Map:

The `.map` method invokes the given block once for each element of the block.

it then creates a new array containing the values returned by the block.  

```
$ a = [ "a", "b", "c", "d" ]
$ a.map { |x| x + "!" } RETURNS ["a!", "b!", "c!", "d!"]
$ a   RETURNS ["a", "b", "c", "d"]

$ my_array = ["fred", "wilma"]
$ my_array.map { |e| e.reverse} RETURNS   ["derf","amliw"]
```

####Reject: 

The method `.reject` returns everything which does not match the block:  
`(1..10).reject { |i| i % 7 == 0} RETURNS   [1,2,3,4,5,6,8,9,10]`

####Inject:

The `.inject` (aka `.reduce`) method combines all the elements of the enumeration by applying a binary operation, specified by a block or a symbol that names a method or operator.

If you specify a block, then for each element in the enumeration the block is passed an accumulator value (memo) and the element. If you specify a symbol instead, then each element in the collection will be passed to the named method of memo. In either case, the result becomes the new value for memo. At the end of the iteration, the final value of memo is the return value for the method.

If you do not explicitly specify an initial value for memo, then the first element of collection is used as the initial value of memo.

Examples:
```
Sum some numbers:  
$ (5..10).reduce(:+)
=> 45
Same using a block and inject:
$ (5..10).inject { |sum, n| sum + n }
=> 45

Multiply some numbers
$ (5..10).reduce(1, :*)
=> 151200
Same using a block:
  (5..10).inject(1) { |product, n| product * n }
=> 151200

Find the longest word:
$ longest = %w{ cat sheep bear }.inject do |memo, word|
$    memo.length > word.length ? memo : word
$ end
$ longest
=> "sheep"
```

####Collect:

The `.collect` (aka `.map`) method returns a new array with the results of running block once for every element in the enumeration.

If no block is given, an enumerator is returned instead.

Examples:

```
$ (1..4).collect { |i| i*i }  RETURNS   [1, 4, 9, 16]
$ (1..4).collect { "cat"  }  RETURNS  ["cat", "cat", "cat", "cat"]
```

####Group_by:

To group our objects, we have a `.group_by` method:  

```
(1..10).group_by { |i| i % 3 == 0}    
RETURNS   {false: [1,2,4,5,7,8,10], true: [3,6,9]}
```

####Cycle:

We can cycle over our enumerator with the `.cycle` method:

```
(1..3).cycle(3) {|i| puts i}
RETURNS   1 2 3 1 2 3 1 2 3
```

####Each_cons:

We also have the `.each_cons method`, which iterates the given block for each array of consecutive <n> elements:

```
(1..10).each_cons(3) { |i| print i} RETURNS 
[1,2,3] [2,3,4] [3,4,5] [4,5,6] [5,6,7] [6,7,8] [7,8,9] [8,9,10]
```

####Each_slice:

The `.each_slice` method iterates the given block for each slice of <n> elements. If no block is given, returns an enumerator.

```
(1..10).each_slice(3) { |a| p a }
RETURNS [1, 2, 3] [4, 5, 6] [7, 8, 9] [10]
```

In-class examples

```
$ valid = []
$(0..10).to_a.each do |value|
$  if value % 3 == 0
$    valid.push value
$  end
$ end
```

```
$ valid = []
$(0..10).to_a.each do |value|
$  valid.push(value) if value % 3 == 0
$ end
```

```
$ valid = (0..10).to_a.select{ |value| value % 3 == 0}
```

```
$ valid = []
$ (0..10).to_a.each do |value|
$   new_value = value*3
$   valid.push new_value
$ end
```

```
$ def gimme_an_array
$   ["cat", "dog", "fish"]
$ end
```
```
$ valid = gimme_an_array.to_a.collect{ |value| "#{value} is cool" }
$ valid = ["cat1", "cat2"].each{ |value| "cat" }
```
```
$ instructors = {
$   instructor1: "Gerry",
$   instructor2: "Jon",
$   instructor3: "David",
$   instructor4: "Julien",
$ }
```

```
$ ["cat", "dog", "fish"].each_with_index do |value, index|
$   puts "the index of #{value} is #{index}"
$ end
```
```
$(1..10).to_a.find_all{ |value| value % 3 == 0}
```

###ADDITIONAL RESOURCES

Ruby Blocks and Enumerables:  
<http://ruby-doc.org/core-2.0.0/Enumerable.html>  
