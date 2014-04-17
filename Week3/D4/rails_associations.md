#ACTIVERECORD ASSOCIATIONS


Associations between models make common operations simpler and easier in our code. To understand this, let’s consider a simple Rails application that would include a model for books and a model for bookshelves. Each bookshelf can have lots of (many) books, and a book belongs to a bookshelf. There are ways to build such a relationship.

There is 6 differents types of associations:   

* belongs_to ...
* has_one ...
* has_many ...
* has_many … :through …
* has_one … :through ...
* has_and_belongs_to_many ...


###Example: Authors, Books, Bookshelves $ Librairies


We're going to create a rails app with lots of models, that we’ll use to illustrate each type of association. Let’s first set up our app.

In terminal:  
`$ rails new bookstore`  
`$ rails g migration CreateBooksAndBookshelves`  

Now in our "db/migrate" folder:

```
class CreateBooksAndBookshelves < ActiveRecord::Migration
  def change
    create_table :bookshelves do |t|
      t.string "name"
      t.integer "quantity"
    end

    create_tabe :books do |t|
      t.string "title"
      t.integer "number_of_pages"
      t.belongs_to :bookshelf
    end
  end
end
```

Now back in terminal:  
`$ bundle exec rake db:migrate`

In "app/models", let’s create a "book.rb" file:  

```
class Book < ActiveRecord::Base
  belongs_to :bookshelf
end
```

Let’s also create a "bookshelf.rb" file: 

```
class Bookshelf < ActiveRecord::Base
  has_many :books
end
```

Now in the rails console, to demonstrate how this works, we can look at the newly created relationship, and see how it impacts our objects:  
`$ bundle exec rails console`  
`$ book.bookshelf` 
`⇒ nil `   
`$ bookshelf.books`  
`⇒ []`  
`$ book.bookshelf.quantity`  
`⇒ nil`  
`$ book.bookshelf.quantity = 200`  
`⇒ 200`  
`$ book.save`  

`$ bookshelf = Bookself.new`  
`$ book1 = Book.new`  
`$ book1.title = "book1"`  
`$ book2 = Book.new`  
`$ book2.title = "book2"`  
→ we now have two different instances of Book. We also have a bookshelf, which is an empty array at the moment.

We can now assign those books to the bookshelf:  
`$ bookshelf.books.push book1`  
`$ bookshelf.books.push book2`  
`$ bookshelf.books`  
`⇒ [book1, book2]`  
`$ bookshelf.save`  
→ now the association has been created, and is persisted in our database.

How do the other associations work?


###has_one

This sets up a one-to-one connection - essentially the same as "belongs_to", but it implies that the foreign-key is on the associated table. It is used when there is only one Thing connected to one OtherThing.

For instance, a Car has_one SteeringWheel, and a SteeringWheel belongs_to a Car.

Sometimes, which side "has_one" and which side "belongs_to" can be contentious.

Let’s add a Library model and an Address model to our app (at this point, we create the "library.rb" and "address.rb" files).

In "library.rb":

```
class Library < ActiveRecord::Base
  belongs_to :address
end
```

In "address.rb":  

```
class Address < ActiveRecord::Base
  has_one :library
end
```

Then in terminal:  
`$ rails g migration CreateLibrairies`

The corresponding migration for Libraries might look like this:

```
class CreateLibraries < ActiveRecord::Migration
  def change
    create_table :libraries do |t|
      t.string :name
    end

    add_column :bookshelves, :library_id, :integer
  end
end
```

The corresponding migration for Addresses might look like this:  

```
class CreateAddresses < ActiveRecord::Migration
  def change
    create_table :addresses do |t|
      t.string :street
      t.string :postcode
      t.string :city
      t.timestamps
    end

    add_column :libraries, :address_id, :integer
  end
end
```

###belongs_to

This sets up a connection between one model and another - it's the table upon which the foreign key resides. We set up our Book model to belong_to Bookshelf
      
In "book.rb": 

```
class Book < ActiveRecord::Base
  belongs_to :bookshelf
end
```

###has_many

This establishes a one-to-many relationship.
We’ve established that our Bookshelf has_many Books. As we’ve seen earlier, this is how we set the "has_many" association.

In "bookshelf.rb":  

```
class Bookshelf < ActiveRecord::Base
  has_many :books
end
```

In "book.rb": 

```
class Book < ActiveRecord::Base
  belongs_to :bookshelf
end
```

As we saw above, our migration file looks like this:

```
class CreateBooksAndBookshelves < ActiveRecord::Migration
  def change
    create_table :bookshelves do |t|
      t.string "name"
      t.integer "quantity"
      t.timestamps
    end

    create_table :books do |t|
      t.string "title"
      t.integer "number_of_pages"
      t.string "author"
      t.belongs_to :bookshelf
      t.timestamps
    end
  end
end
```

###has_many :through

This sets up a many-to-many relationship "through" another associated model.
A good illustration of this would be: a Game and Person could join through a "Playing" model. This would allow a Game to have_many Players (People), and for a Person to be Playing many Games.

In our current example though, we can observe the following interaction.
In "library.rb":

```
class Library < ActiveRecord::Base
  has_many :bookshelves
  has_many :books, through: :bookshelves
end
```

In "bookshelf.rb":  

```
class Bookshelf < ActiveRecord::Base
  belongs_to :library
  has_many :books
end
```

In "book.rb":

```
class Book < ActiveRecord::Base
  belongs_to :bookshelf
  has_one :library, through: :bookshelf
end
```

A "has_many :through" association is often used to set up a many-to-many connection with another model. 

This association indicates that the declaring model can be matched with zero or more instances of another model by proceeding through a third model.

A has_one :through association sets up a one-to-one connection with another model. 

This association indicates that the declaring model can be matched with one instance of another model by proceeding through a third model.

The corresponding migration might look like this:

```
class CreateBooks < ActiveRecord::Migration
  def change
    create_table :libraries do |t|
      t.string :name
      t.timestamps
    end

    create_table :bookshelfs do |t|
      t.string :name
      t.string :quantity
      t.belongs_to :library
      t.timestamps
    end

    create_table :books do |t|
      t.belongs_to :bookshelf
      t.string :name
      t.string :author
      t.timestamps
    end
  end
end
```

Now the models can be associated automatically  
`$ library = Library.new`

New join models are created for newly associated objects, and if some are gone their rows are deleted.
⇒ Automatic deletion of join models is direct, there is no destroy callbacks being triggered.


###has_and_belongs_to_many


A "has_and_belongs_to_many" association creates a direct many-to-many connection with another model, with no intervening model. 

For instance, a Person can drive many Cars, and a Car can be driven by many People. It's a "traditional" join-table design - with two foreign keys. 

However, this type of association is fairly constraining. If you need any more information about the relationship between drives and cars, for instance (like the date the driving occurred, or how far the trip was), then a different association type might be better suited for the task.

If you create a has_and_belongs_to_many association, you need to explicitly create the joining table. 

Unless the name of the join table is explicitly specified by using the :join_table option, ActiveRecord creates the name by using the lexical order of the class names. 

So a join between Customer and Order models will give the default join table name of "customers_orders" because "c" outranks "o" in lexical ordering.

In the context of our example, let’s create "author.rb":  

```
class Author < ActiveRecord::Base
  has_and_belongs_to_many :books
end
```

And in "book.rb":

```
class Book < ActiveRecord::Base
  has_and_belongs_to_many :authors
end
```

The corresponding migration might look like this: 

```
class CreateBooks < ActiveRecord::Migration 
  def change 

    create_table :authors do |t| 
      t.string :name 
      t.timestamps 
    end

    create_table :authors_books, id: false do |t|
      t.integer :author_id
      t.integer :book_id
    end

    create_table :books do |t|
      t.belongs_to :bookshelf
      t.string :name
      t.string :author
      t.timestamps
    end

  end
end 
```

###has_one :through


Finally, this type of relation gives us a single-association to another model, but through a third model.

We can reciprocate from Delivery Addresses to set it to be able to access the Customer through its Order.




##Another example: Customers & Orders


Consider another simple Rails application that includes a model for customers and a model for orders. Each customer can have lots of (many) orders, and a order belongs to a customer.

Create a new associations rails app.  
`$ rails new associations`  
`$ cd associations`  


###Has_many association

Create a customer and order model, and migrate the database.  
`$ rails g model Customer name:string`

The customer_id field is how we are going to associate our customers with their order. This is a has_many association.

In terminal:  
`$ rails g model Order customer_id:integer value:decimal`  
`$ rake db:migrate`

![has_many example](https://lh6.googleusercontent.com/WVoV9O0pffSKgQeZMqZgPRNMlomSjBlIhsZ__krH-uHDVJLr6uuTqOF7-HWRBiG4EdXHuK4sgG5_slIXRT00T7DZRSeF2st-e2MNrJ1aM9HrqGJfb4ScPdlk-g)

Our models for customer and order will look like this.
In "customer.rb":  

```
class Customer < ActiveRecord::Base
  has_many :orders
end
```

In "order.rb":

```
class Order < ActiveRecord::Base
  belongs_to :customer
end
```

###Creating Associations in terminal:

Let’s have another at how we create relations within the terminal. We can go into our rails console and create associations between orders and customers.  
`$ rails c`

Create three new customers.   
`$ c=Customer.create({name: "jon"})`  
`$ c2=Customer.create({name: "gerry"})`  
`$ c3=Customer.create({name: "david"})`  

We can check all of our customers have been added using:  
`$ Customer.all`

Now we can create orders and associate our customer id’s with each order.  
`$ Order.create({customer_id: c.id, value: 10.32})`  
`$ Order.create({customer_id: c.id, value: 15.73})`  
`$ Order.create({customer_id: c2.id, value: 1.29})`  
`$ Order.create({customer_id: c2.id, value: 99.99})`  
`$ Order.create({customer_id: c.id, value: 110.33})`  
`$ Order.create({customer_id: c.id, value: 21.15})`  
`$ Order.create({customer_id: c2.id, value: 201.91})`  
`$ Order.create({customer_id: c3.id, value: 98.89})`  

Now that associations are set up, we can find all the orders which are associated with David.  
`$ Order.where customer_id: c3.id`


##Destroying Dependants

If we delete Gerry:  
`$ c2.destroy`  
We notice that all of his orders will remain stored in our database, albeit with no association to a specific customer. 

To avoid this situation, we need to tell Rails to destroy associations manually. We can do this in the customer model by using the following code:  
`dependent: :destroy`

In "customer.rb":

```
class Customer < ActiveRecord::Base
  has_many :orders, dependent: :destroy
end
```

No if we go into the console again, we can count all the orders:  
`$ Orders.count`

And then delete the customer Jon:  
`$ Customer.first.destroy`

Now if we check the order count again → the number is significantly lower. That’s because we’ve gotten rid of Jon’s orders as well.

Lets add a "DeliveryAddress" model to our app:  
`$ rails g model DeliveryAddress directions:text`  
`$ rake db:migrate`

In "delivery_address.rb":

```
class DeliveryAddress < ActiveRecord::Base
   has_one :order
end
```

Now we will create a migration for this:  
`$ rails g migration AddDeliveryAddressIdToOrders delivery_address_id:integer`  
`$ rake db:migrate`

We can now update our "order" model.
In "order.rb":

```
class Order < ActiveRecord::Base
  belongs_to :customer
  belongs_to :delivery_address
end
```

We can set our Customer to have_many DeliveryAddresses through Orders.
In "customer.rb":

```
class Customer < ActiveRecord::Base
  has_many :orders, dependent: :destroy
  has_many :delivery_addresses, through: :orders
end
```

Now we can go back into the console and create a new delivery address:   
`$ d = DeliveryAddress.create({Directions: "Turn left"})`

Pro-tip: we can also assign an order to a variable so we can work with it:  
`$ o = Order.first`

Now we can get all of the delivery address for a customer, through their order:  
`$ o.customer.delivery_addresses`


###Self-referential Join

This is a bit of a special case, and it is good to know that it exists: we can also associate a model to itself. 

To illustrate this, let’s create an Employee model and migrate our database:  
`$ rails g model Employee name:string manager:references`  
`$ rake db:migrate`  

Now we can go into the console and create two new employees:  
`$ rails c`  
`$ Employee.create name: 'Bob'`  
`$ Employee.create name: 'John'`  

We don't have a Manager model to associate our employees with - in this case, our managers are already in the DB as employees themselves.

Rails allows us to set up our foreign-key field, as well as an association in Employee.

In "employee.rb":  

```
class Employee < ActiveRecord::Base
  belongs_to :manager, class_name: 'Employee'
  has_many :subordinates, class_name: "Employee", foreign_key:"manager_id"
end
```

Now we can go back into our console, and associate our employees with each other:  
`e = Employee.first`  
`m = Employee.last`  
`e.manager = m`  
`e.save`  


##Other in-class example: Garage & Car


Considering two models, Car and Garage…

We have a "garage.rb" file:  

```
class Garage < ActiveRecord::Base
  has_many :cars
end
```

And a "car.rb" file:

```
class Car < ActiveRecord::Base
  belongs_to :garage
end
```

In terminal, we can:   
`$ car = Car.new`  
`$ garage = Garage.new`  

`$ garage.cars `  
`#=> []`  
`$ car.garage`  
`#=> nil`

And we can look at how those two models interact:  
`$ car.garage = garage`  
`$ garage.cars = car `  
`# throws an error`  
`$ garage.cars.push(car)`  
`$ garage.cars << car`  
`$ garage.cars = [car]`  

#ADDITIONAL RESOURCES

ActiveRecord Associations:  
<http://guides.rubyonrails.org/association_basics.html>