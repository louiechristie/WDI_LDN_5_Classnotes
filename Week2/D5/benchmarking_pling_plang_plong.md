## Pling Plong Plang solutions

###Mike's solution

```
def ppp_rec(i, nos)
  if i == (nos + 1) then 
    print ""
  else
    output = ""
    if i % 3 == 0
        output << "Pling"
    end
    if i % 5 == 0 
        output << "Plang"
    end
    if i % 7 == 0
        output << "Plong"
    end
    if output == "" 
      output = i.to_s
    end
    output << ","
    print output
    ppp_rec(i + 1, nos)
  end 
end
```

###Scotty's solution 

```
while number<=2000000
  if number%105==0
    print("PLING PLANG PLONG,")
  elsif number%35==0
    print("PLANG PLONG, ")
  elsif number%21==0
    print("PLING PLONG, ")
  elsif number%15==0
    print("PLING PLANG, ")
  elsif number%7==0
    print("PLONG, ")
  elsif number%3==0
    print("PLING, ")
  elsif number%5==0
    print("PLANG, ")
  else
    print "#{number}, "
  end
  number=number+1
end
```

###Jason's solution

```
hash = {3=> "Pling", 5=> "Plang", 7=> "Plong"}
 
(1..100).each do |i|
  if i % 3 != 0 && i % 5 != 0 && i % 7 != 0
    puts i
  else
    hash.each do |number, drop|
      if i % number == 0
        print drop
      end
    end
  end
  puts
end
```




# Benchmarking

Benchmark is the act of running a computer program, a set of programs, or other operations, in order to assess the relative performance of an object, normally by running a number of standard tests and trials against it. 

Example from pling plang plong :

```
require 'benchmark
Benchmark.bmbm do |b|
  b.report("MGP's Pling Plang Pling") do
    10.times do
      do_the_pling_plang
    end
  end
end 
```
