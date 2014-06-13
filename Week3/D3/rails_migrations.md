#RAILS MIGRATIONS


Migrations are a way for us to manage the creation and alteration of our database tables in a structured and organized manner.

Each migration is a separate file, which Rails runs for us when we instruct it. Rails keeps track of what's been run, so changes don't get attempted more than once.

We describe the DB changes using Ruby, and it doesn't matter which DB engine we use. Rails has connectors for each different DB engine we might use, which translates the ruby structure into the appropriate DB commands.

We will make a new migration, let start by creating a new rails app.

Into our "w3d2/classwork" folder, run the command:  
`$ rails new bookshelf`  
`$ cd bookshelf`  

Creating a table:

Now that we’ve created our app, let’s create a migration:  
`$ rails generate migration CreateBooks`

Let’s open our app in sublime:  
`$ subl .`  

Now if we look at our folder, in db/migrate, we have a file named "20130702101500_create_books.rb" file (mostly empty for now), in which we can  write out your migration:  

```
class CreateBooks < ActiveRecord::Migration
  def up
    create_table :books do |t|
      t.string :title
      t.integer :page_number
      t.string :author
      t.text :content  
    end
  end
  def down
    drop_table :books
  end
end
```

Or we can write this by following a much simpler syntax. We can delete the "down", and change the "up" to "change". This tells ActiveRecord to do the opposite of up if we are dropping the database.

Example:

```
class CreateBooks < ActiveRecord::Migration
  def change
    create_table :books do |t|
      t.string :title
      t.integer :page_number
      t.string :author
      t.text :content  
    end
  end
end
```

FYI, we can pass a range of different arguments to each field in our table to customize it further:  

* null: true or false
* limit: sets the maximum size of the string/text/binary/integer fields
* precision: defines the precision for the decimal fields
* scale: defines the scale for the decimal fields
* polymorphic
* etc.

We can now run our rake command to migrate the database:  
`$ bundle exec rake db:migrate`  
→ behind the scenes, rails has been using SQL statements to create a table in our database. 

Pro-tip: it is always a good idea to check the output from the console to ensure that the migration has run without any errors.

Let’s create a second object, "Reader":  
`$ bundle exec rails g scaffold Reader`
→ this creates everything we need in our app for Reader.

Let’s edit the migration file:

```
class CreateReaders < ActiveRecord::Migration
  def change
    create_table :reader do |t|
      t.string :firstname
      t.date :birthdate
      t.text :bio
      t.timestamps
    end
  end
end
```

If we wanted to get rid of Reader, we can reverse our command:  
`$ bundle exec rails d scaffold Reader`  
→ here, "`d`" stands for "delete".

 
###Adding columns:

Now if we want to update our Books table, we need to write a new migration. This time, we will use a rails generator to help us write our migration. In the command line run:

`$ bundle exec rails g migration AddPublishingToBooks publishing:string`  
→ this tells rails to create our migration file for us. 

In our migration file:  

```
class AddPublishingToBooks < ActiveRecord::Migration
  def change
    add_column :books, :publishing, :string
  end
end
```

By using this convention ("Add....To...."), Rails just *knows* which column ("publishing") we are adding to which table ("books) with which type of data ("string"), so the migration can be written automatically.

If the migration name is of the form "AddXXXToYYY" or "RemoveXXXFromYYY" and is followed by a list of column names and types, then a migration containing the appropriate `add_column` and `remove_column` statements is created. 

We go ahead and add our column to our table by running:  
`$ bundle exec rake db:migrate`

There are many more column/data types that we can use:  
   
        :binary  
        :boolean  
        :date  
        :datetime  
        :decimal  
        :float  
        :integer  
        :primary_key  
        :string  
        :text  
        :time  
        :timestamp  


###Removing columns:

We can use the same naming convention as "create" to automatically generate the migration file:  
`$ bundle exec rails g migration RemovePublishingFromBooks name:string`


###Changing a column:

To change the data type or the options of a field, we need to use the following command in our migration file:  
`change_column :books, :column_name :type`


###Renaming a column / renaming a table:

To rename tables or columns, we use the following commands in our migration file:  
`rename_table :old_name, :new_name`  
`rename_column :table_name, :old_name, :new_name`


###Rake actions:

We can think of "rake" as a task manager for ruby. It receives our instructions on what to do with the files we’ve written.

As we’ve seen so far, to run migrations, we simply type:  
`$ bundle exec rake db:migrate`

We can undo our migration by using the command:  
`$ rake db:rollback`  


Pro-tip: we can only use this rollback command if the migrations have been run on our own machine. 

That means that if they have been run on a different machine, we will need to write a new migration to undo the changes.


###A few words on "schema.rb":

When we run migrations, Rails also updates the "schema.rb" file. The "schema.rb" file contains all the migration commands that have been run to create our into tables.

We can view this table by using the command  
`$ cat db/schema.rb`


###A few words on Models and Migrations:

Since ActiveRecord models map to tables, when you create a model, it should automatically create a table migration.

So if we generate our model from the command line:  
`$ bundle exec rails g model Customer name:string description:text active:boolean credit_limit:decimal last_contact_at:datetime`

… a migration gets automatically created for us:   

```
class CreateCustomers < ActiveRecord::Migration
  def change
    create_table :customers do |t|
      t.string :name
      t.text :description
      t.boolean :active
      t.decimal :credit_limit
      t.datetime :last_contact_at
      t.timestamps
    end
  end
end
```

#ADDITIONAL RESOURCES

Rails Migrations:  
<http://apidock.com/rails/ActiveRecord/ConnectionAdapters/SchemaStatements/change_column>
