#DATABASES


##What is a database?

A database is an organized collection of data. The data is typically organized to model relevant aspects of reality. We will be using databases to persist the data we are using in our apps. You will also hear a database being referred to as a schema.

For now, we will be working solely with SQL databases - also known as "relational databases". We can understand the concept of relational database when picturing it as a **table structure**. 

While this is fairly easy to grasp, this model has limitations: we need to contrive our data to fit it into this table structure. There are other types of databases which allow for different types of data structures.


###Postgresql

Postgres is an SQL database.

To install postgresql, we can use brew:

```
$ brew install postgresql
$ initdb /usr/local/var/postgres -E utf8
$ mkdir -p ~/Library/LaunchAgents
$ ln -sfv /usr/local/opt/postgresql/*.plist ~/Library/LaunchAgents
$ launchctl load ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
```

###SQL

To send and receive data from our database, we use SQL to query it.

To check everything is working we type the command:  
`$ psql`  
→ if all is working, this will return our psql version to us.

To get more information and help we type the command:  
`$ help`

To see a list of the commands available to us in psql:  
`$ \?`

To exit psql:  
`$ \q`

To create a database we use the command:  
`$ create database database_name`

To delete the database we use the command:  
`$ drop database database_name`

To connect to our database we can use the command:  
`$ \c database_name`

To get a list of our database relationships we type:  
`\d`

To get an explanation of our table we use:  
`\d table_name`

To create a table we use:  

```
$ create table people (
  name varchar(255),
  age int2,
  sex char(1)
  );
```

`varchar`, `int2` and `char` are different kinds of data types which we can store in our database. For more datatypes, look at: <http://www.postgresql.org/docs/8.4/static/datatype.html>

Convention: never use CamelCase in SQL!

**SQL Keywords (don't use them as field names!):**

* create
* drop
* database
* table
* select
* insert
* into 
* values
* update
* where
* delete
...  

###CRUD in SQL

CRUD stands for Create, Read, Update and Destroy. 

To read the data from this table we use a select command:  
`select * from people;`  
→ this is telling the database to retrieve all information from our "people" database.

`select age from people;`  
→ this is telling the database to retrieve all  information from the "age" column of our "people" database.

To create data in our table we can use an insert command:  
`insert into people (name) values ('Fred');`
→ this command puts the name 'Fred' into the "name" column of our "people" table.

`insert into people (age) values (35);`  
→ this command puts the age 35 into the next row of the "age" column of our "people" database.

To create data to put into multiple columns in one command we can use:  
`insert into people (name, age) values ('Barney', 26);`

We can update our data by using an update statement:  
`update people set age =41 where name = "Fred";`

Or we can update multiple columns by using:  
`update people set age =40 ='m' where name = "Fred";`

If you don't specify where you want to update, all rows of the database will be updated. 

We can destroy data from our table by using:  
`delete from people where age=35;`  
→ again if we are not explicit about where, all rows of our database will be deleted.


###Primary Keys

In the following example, we will create an ID column. Each entry in our ID column will be unique. This means we can use this value to select our rows without the danger of selecting multiple or wrong entries. We call this our **Primary Key**.

```
  CREATE TABLE weather (
    id serial8 primary key,
    city varchar(255) unique not null,
    low int default 0,
    high int check (high > low),
    high_recorded_on date,
    low_recorded_on date
   );
```

Note: this table has some additional checks and default values. Our "city" must be unique and present, our "low" will default to null,  and our "high" must be more than our "low".

```
 INSERT INTO weather (city, low, high, high_recorded_on) VALUES ('London', -10, 43, '1 Jan 2012');
 ```

Even though we did not tell the database to add an ID, it will be automatically entered for us as consecutive integers.

####Another example of Foreign Keys

In the following example, we will create a "campus" database with students and courses. 

The tables we will use are "Students", "Courses" and "Schedules". We will use a join table (schedule), which will store the relationships between "students" and "courses".


**Students**

id | first | last | dob | gpa
---|-------|------|-----|----
1 | 'bob' | 'martin' | 19-04-85 | 1 
2 | 'kelly' | 'shaw' |21-01-89 | 2


**Courses**

id | name  
---|------
1| english
2 | maths


**Schedules** (we have two foreign keys in this table which relate to the id's of students to the id's of courses they are taking).  

id | student_id | course_id
---|------------|----------
1 |1 |1
2 |1 |1
3 |2 |2
4 |2 |2


Create a file called campus.sql and enter:  

```
CREATE TABLE students
 (
   id serial4 primary key,
   first varchar(20),
   last varchar(20),
   dob date,
   gpa float4
 );
 
 CREATE TABLE courses
 (
   id serial4 primary key,
   name varchar(50) unique not null
 );
 
 CREATE TABLE schedules
 (
   id serial4 primary key,
   student_id int4 references students(id),
   course_id int4 references classes(id)
 );
```

To go into our campus database, we use:  
`$ psql campus`

To then create some tables in our campus database using our file "campus.sql". For this, we use the command:   
`$ psql -d campus -f campus.sql`

We will populate our "students" table with some students:  

`INSERT INTO students (first, last, dob, gpa) VALUES ('bill', 'jones', '1/1/1990', 3.1);`  
`INSERT INTO students (first, last, dob, gpa) VALUES ('sally', 'jones', '1/1/1950', 3.6);`  
`INSERT INTO students (first, last, dob, gpa) VALUES ('sue', 'smith', '1/1/2013', 2.6);`  

And populate our "courses" table:  
`INSERT INTO courses (name) VALUES ('Biology');`  
`INSERT INTO courses (name) VALUES ('Chemistry');`  
`INSERT INTO courses (name) VALUES ('Physics');`  

Then we populate our "schedules" table to create relationships between our courses and students:  
`INSERT INTO schedules (student_id, class_id) VALUES (2, 3);`  
`INSERT INTO schedules (student_id, class_id) VALUES (2, 1);`  
`INSERT INTO schedules (student_id, class_id) VALUES (2, 2);`  

How do you find out the names of the students taking Biology (class_id 1)? We need to use a select query to do this.  
`SELECT id FROM courses where name = 'Biology'`  
`SELECT student_id from schedules where class_id = 1;`  
`SELECT * from students where id in (2,3)`  

We can nest our statements to do the same as the three lines above:  
`SELECT * from students where id in (select student_id from schedules where class_id = 1);`

We can read more about this at <http://www.w3schools.com/sql/>




##LAB: DATABASES WITH RUBY (Facebook App)


We will go through the steps to create our own facebook app. We will create a database,  insert some people into it, then create a ruby file to "talk" to our database. We will then have the ruby program print from the database, and insert into the database.

Step by step:

```
$ mkdir facebook
$ touch facebook.sql
$ cd facebook
$ subl facebook.sql
```

In our "facebook.sql", type:

```
   CREATE TABLE people
  (
    id serial8 primary key,
    first varchar(20) not null,
    last varchar(20) unique not null,
    dob date check (dob < '1/1/2000'),
    relationship varchar(50),
    friends int2 default 0,
    city varchar (20)
  );
```

```
$ psql facebook
$ psql -d facebook -f facebook.sql
$ psql facebook
$ \d facebook
```

Then, type:  
`INSERT INTO people (first, last) VALUES ('jim', 'jones');`  
`INSERT INTO people (first, last) VALUES ('sue', 'smith');`  
`INSERT INTO people (first, last, dob) VALUES ('jil', 'nance', '1/1/2003');`  

This should not work, as the date of birth is too recent for the field's constraint.  
`SELECT * FROM people;`

Finally, to exit:  
`\q` 


####Let's install PostgreSQL

To install the PostgreSQL gem, go to your terminal (stay in the same folder for the example).  

`$ gem install pg`  
`$ touch main.rb`  
`$ subl main.rb`  

In our "main.rb" file, type:  
`require 'pry'`  
`require 'pg'`  

`db = PG.connect(:dbname =>'facebook', :host => 'localhost')`  
→ This line can be inside or outside of the begin/ensure/end statement (just after "begin").

Then:  

```
begin
 db.exec( "select * from people" ) do |result|
   result.each do |row|
     # do stuff
   end
 end
ensure
 db.close
end
```

We can can get our Ruby code to print out the names from the rows in our database by changing our "main.rb" code to:  
`require 'pry'`  
`require 'pg'`  

`db = PG.connect(:dbname =>'facebook', :host => 'localhost')`

```
begin
 db.exec( "select * from people" ) do |result|
   result.each do |row|
     puts "#{row['first']}#{row['last']}"
   end
 end
ensure
 db.close
end
```

We can then take user input and insert it into our database by changing our code to: 

```
require 'pry'
require 'pg'
```

`db = PG.connect(:dbname =>'facebook', :host => 'localhost')`

```
begin
 db.exec( "select * from people" ) do |result|
   result.each do |row|
     puts "#{row['first']}#{row['last']}"
   end
 end

  print "full name"
  name = gets.chomp.split
  print "dob"
  dob = get.chomp
  print "rel:"
  relationship = gets.chomp
  print "city:
  city = gets.chomp
  sql = "insert into people(first, last, dob, relationship, city) values ('#{name[0]}',     '#{name.join(" ")}', '#{dob}', '#{relationship}', '#{city}')"
ensure
 db.close
end
```

###ADDITIONAL RESOURCES


Databases: 
<http://www.postgresql.org/docs/8.4/static/datatype.html>  
<http://www.w3schools.com/sql/>