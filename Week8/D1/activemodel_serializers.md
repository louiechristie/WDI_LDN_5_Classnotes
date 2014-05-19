# ActiveModel Serializers

Start by creating a new rails app:

    rails new serializer
    cd serializer

Now create scaffolding for an author, comment and a post:

    rails g scaffold author first_name last_name email biography:text
    rails g scaffold post author:references title content:text
    rails g scaffold comment first_name last_name content:text post:references

In the _form for posts. We will change the field for the author to author_id

    <%= f.collection_select :author_id, Author.all, :id, :first_name %>

In the _form fo comments. We will change the field for the post to post_id

    <%= f.collection_select :post_id, Post.all, :id, :title %>

We will need to update our Post model to add:

    has_many :comments
    attr_accessible :content, :title, :author_id

We will also need to update our Author model to add:

    has_many: posts

And we will need to update our comment model to include:

    attr_accessible :content, :first_name, :last_name, :post_id

Now we can migrate our database, and run the server. 
Go and create some authors, posts and comments...


## JSON

We have seen that our Rails' apps scaffold adds JSON support by default. 
  - Where does it do this?
  - What does the output look like? 
  - How could we customize this?
    - build a hash ourselves
    - use a gem that does it for us


## Serializers

We will use the activeRecord serializer gem. The purpose of ActiveModel::Serializers is to provide an object to encapsulate serialization of ActiveModel objects, including ActiveRecord objects.

Serializers know about both a model and the current_user, so you can customize serialization based upon whether a user is authorized to see the content.

In short, serializers replace hash-driven development with object-oriented development.

Add the activemodel serializer gem to your gemfile:

    gem 'active_model_serializers'

And `bundle`. 


If we type `rails g` in our console, we will see we have a new serializer option. We can use this on the author:

    rails g serializer author

We will now have a new folder in our app called serializer. Now if we restart our server and go to:

    localhost:3000/authors.json

We will only be returned our authors ids in JSON. 


To change this output go to serializers/author_serializer.rb we can update our attributes to control our JSON output:

    attributes :id, :first_name, :last_name

Now JSON format requests will be returned our authors' id, first and last name in JSON. 

We can also define our relationships in the serialize, we do this the same way as in the model:

    has_many :posts

If we donâ€™t want our JSON to display all of our post model, we can specify the attribute we want to display. We can control this using embed 

    has_many :posts, embed: :ids

Or you can generate a `post_serializer`.

We can add new properties using methods. For example we can make a full name method in the serialize:

    def full_name 
      "#{object.first_name} #{object.last_name}"
    end

or in the model itself:

    def age
      21
    end

And add this to the serializer's attributes to use it:

    attributes :full_name, :age


To get rid of the root in our JSON output, we can go to AuthorsController and after our response to format json:

    format.json { render json: @authors, root: false }

Setting `root` to `true` doesn't render it again though... but rather, controls the JSON property that is assigned to the collection.
