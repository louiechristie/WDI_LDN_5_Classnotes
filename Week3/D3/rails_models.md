#RAILS MODELS


Rails give us an interface to interact with the DB: this interface is one of rails' many gems, and is called ActiveRecord.

ActiveRecord is the "parent" of all the models we create in our rails application. It provides these models with a lots of functionalities (since our models inherit from ActiveRecord).

[ActiveRecord](http://guides.rubyonrails.org/v3.2.13/active_record_querying.html ) base is the ORM (Object Relational Mapper) we will be using. The ORM is responsible for converting data between incompatible type systems in object-oriented programming languages.

In Ruby, one table maps to one Ruby class, then columns map to methods, and are inferred from the schema.

Let’s look at the Book model. It’s already in our app, because we scaffolded it earlier. 

If needed, to create the "book.rb" file:

```
$ touch app/models/book.rb
$ subl .
```

We can edit our book model. Open "app/models/book.rb":

```
class Book < ActiveRecord::Base
  attr_accessible :title
end
```

Now we can go into pry:  
`$ bundle exec rails console`

Pro-tip: when modifying our files in Sublime, instead of exiting the console and relaunching it, we have this handy shortcut command:  
`$ reload!`


###Creating records:

Let’s create our first book.  
`$ book = Book.new`  
`$ book.content = "blabla"`  
`$ book.title= "WDI"`  
`$ book.save`

For the sake of saving some time when populating your database, to create multiple (generic) instances at once:  
`$ (1..10).to_a.each{|time| book = Book.new(title,: "title#{time}"); book.save; }`

Again, to create a new book in memory:  
`b = Book.new`

Now assign some values:  
`b.title = "Star Wars"`  
`b.release_date = 1977`  

Now save it to the database:  
`b.save`  
→ when we save the book instance to the database, it will be assigned and id.

We can write this in a shorter form:  
`m = Book.new(title: "The Empire Strikes Back")`  
`b.save`

Or we can use "create", which will create a new instance of book in memory and save it in one command. 

We will need to whitelist the title in the model before we are able to do this though:  
`b = Book.create(title: "The Return of the Jedi")`


###Finding records:

If we want to view our books:  
`$ Book.all`

ActiveRecord allows us to find elements by their id. We can use the command:  
`$ Book.find(5)`
→ this will return to us the single record with the id of 5 (provided such a record has been created).

We can also find our books by title:  
`Book.find_by_title('Tom Sawyer’) `

And find multiple books by title:  
`Book.find_all_by_title('Tom Sawyer')`

To return multiple entries we can also use "where":  
`Book.where(release_date: Date.today-2.years))`
or
`$ Book.where(["release_date > ?", Date.today-2.years])`

`.where` will always return an array, even if there’s only one corresponding entry.

It is also possible to order our returned results.  
`$ Book.order(:title)`

We can also chain these methods. For example, we could write:  
`$ Book.where(release_date: %w(1999 1994 1986)).order(:title)`


Additional examples of queries:

`$ Book.where("released_date > ? AND released_date < ?", 2.years.ago, 1.years.ago)`

`$ Book.where('title != ?', 'title1')`  
→ this returns the books with a title different than "title1".

`$ Book.select("title").where(author: "Julien")`

`$ Book.all.limit(2)`  
→ this returns only the two first results of our list.

`$ Book.where(title: "something").limit(2).offset(3)`  
→ let’s say we have 10 books with the title "something". This specific query would return the first two results matching "something" AFTER the third position in the list of 10 (so it returns the fourth and fifth entries). Think of Google result pages, returning groups of results offset by 10 and limited to 10 by page. 

`$ Book.find_by_sql("SELECT * FROM books")`  
→ this selector allows you to use a SQL request directly.

Now some of these queries might look overly complex - fear not, we usually don’t use such queries often. But it is good to know that they exist, and that the querying system offers a very rich, complete and flexible way to target specific instances with a high degree of precision.


###Updating records:

Now to update the saved record, we can use:  
`b.update_attributes(title: "Star Wars: A New Hope", release_date: 1983)`


Deleting records:

And to delete it we can use:  
`b.destroy`  
or   
`b.delete`

We should use "destroy" as opposed to "delete". Why?

`:destroy/:destroy_all` → the associated objects are destroyed alongside this object by calling their "destroy" method  
`:delete/:delete_all` → all associated objects are destroyed immediately without calling their "destroy" method (and this doesn’t run the callback).

To check if our record has been persisted we can use the command:  
`m.persisted?`

To check if our record has been change we can use:  
`m.changed?`

Or to view the changes we can use:  
`m.changes`





##MODELS - SCOPES AND CALLBACKS


Scoping allows us to specify commonly-used queries which can be referenced as method calls on the association objects or models. 

With these scopes, we can use every method we’ve seen so far, such as "where", "joins" and "includes". 

All scope methods will return an ActiveRecord::Relation object, which allows for even more methods (such as other scopes) to be called.

Callbacks are methods that get called at certain moments of an object's life cycle. With callbacks, it is possible to write code that will run whenever an ActiveRecord object is created, saved, updated, deleted, validated, or loaded from the database.


Let’s add a "published" column to our books table:  
`$ bundle exec rails g migration AddPublishedToBooks published:boolean`  
`$ bundle exec rake db:migrate`  

Now let’s jump in the console:  
`$ bundle exec rails console`

Then, in the console:  
`$ books = Book.all`  
`$ books.published = true`  

Now, inside the "book.rb", let’s update the model:

```
class Book < ActiveRecord::Base
  attr_accessible :title
  scope :published, -> { where(published: true) }
  scope :unpublished, -> { where(published: false) }
end
```

Adding a scope to our model is the equivalent of hardcoding a specific request into an alias - it makes the code more maintainable, and DRYer. 

Good to know - your scope can take arguments:  

```
class Book < ActiveRecord::Base
  scope :released_before, ->(time) { where("released_date < ?", time) }
end
```

Let’s look at how "unpublished" works in the console:  
`$ Book.unpublished `
→ will return the books which have "published" set to "false".


###before_save & after_save:

Even further, we can customize our scopes a bit more. Example: inside the "book.rb", let’s update the model:  

```
class Book < ActiveRecord::Base
  attr_accessible :title
  scope :published, -> { where(published: true) }
  scope :unpublished, -> { where(published: false) }
  scope :by_author, ->(author) { where author: author) }

before_save do
  puts "This record will be saved soon"
end

after_save do
  puts "You’ve just saved this record"
end

Book.by_author("Julien")

end
```

In the console, when we run `book.save`, the `after_save` and `before_save` scopes get called, and the messages are displayed accordingly.  
`$ book = Book.new`  
`$ book.save`  
→ the two messages were displayed (both before and after the operation)


###Applying a default scope:

We can also ensure that default values are saved where specified. For example, if we forget to mention a specific author when creating a new book instance in the console, we could have this:

```
before_save do
  puts "This record will be saved soon"
  self.author = "no author" if self.author.nil?
end
```

Then, in the console:  
`$ book = Book.new`  
`$ book.author = "Julien"`  
→ saves the book with "Julien" as the author.

However, if we do not mention the author, and proceed with saving:  
`$ book.save`  
→ saves the book with "no author" as the author.


###after_initialize:

We can add an `after_inititalize` scope as well.
Back to our "book.rb", let’s update the model and add:

```
after_initialize do
  puts "You just initialized a new book"
end
```

→ now in the console, every time we do book = Book.new, the message will be displayed.


###before_destroy:

We also have a `before_destroy`:

```
before_destroy do
  "we’re about to destroy this book!"
end
```

Now in the console:  
`$ book = Book.last `
→ returns the last created book instance.


###Unscoped

Should we wish to remove scoping for some reason, we can use the "unscoped" method.   
`$ Book.unscoped.all`  
→ this is especially useful if a "default_scope" is specified in the model, and if this particular "default_scope" should not be applied for a given query.

#ADDITIONAL RESOURCES

Rails Models:  
<http://guides.rubyonrails.org/v3.2.13/active_record_querying.html>  
<http://guides.rubyonrails.org/v3.2.13/>

Models - Scopes & Callbacks:  
<http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html>  
