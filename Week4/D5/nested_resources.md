# Nested Resources

In this article, I'll walk through a basic Rails (3.2.x) setup for creating a nested resource for two models. Nested resources work well when you want to build out URL structure between two related models, and still maintain a RESTful convention. This code assumes you are running RVM to manage Ruby/Gem versions, and Git for version control.

### Creating a new Rails project

In Terminal:  

```
$ rails new family # version control 
$ cd family # install rails 
```

### Create two models (Parent & Child)

```
$ rails generate scaffold Parent name:string 
# Child model 
$ rails generate scaffold Child name:string parent:references 
# Create db (defaults to SQLite3) 
$ rake db:migrate 
# version control 
```

### Review un-nested routes

```
$ rake routes
   children GET    /children(.:format)          children#index
            POST   /children(.:format)          children#create
  new_child GET    /children/new(.:format)      children#new
 edit_child GET    /children/:id/edit(.:format) children#edit
      child GET    /children/:id(.:format)      children#show
            PUT    /children/:id(.:format)      children#update
            DELETE /children/:id(.:format)      children#destroy
    parents GET    /parents(.:format)           parents#index
            POST   /parents(.:format)           parents#create
 new_parent GET    /parents/new(.:format)       parents#new
edit_parent GET    /parents/:id/edit(.:format)  parents#edit
     parent GET    /parents/:id(.:format)       parents#show
            PUT    /parents/:id(.:format)       parents#update
            DELETE /parents/:id(.:format)       parents#destroy
```

### Adding model relationships

- file: app/models/parent.rb

```
class Parent < ActiveRecord::Base
  attr_accessible :name
  has_many :children
end
```

- file: app/models/child.rb

```
class Child < ActiveRecord::Base
  attr_accessible :name, :parent_id
  belongs_to :parent
end

```

### Nesting the routes

- file: config/routes.rb

```
resources :parents do
  resources :children
end
```

```
$ rake routes
  parent_children GET    /parents/:parent_id/children(.:format)          children#index
                  POST   /parents/:parent_id/children(.:format)          children#create
 new_parent_child GET    /parents/:parent_id/children/new(.:format)      children#new
edit_parent_child GET    /parents/:parent_id/children/:id/edit(.:format) children#edit
     parent_child GET    /parents/:parent_id/children/:id(.:format)      children#show
                  PUT    /parents/:parent_id/children/:id(.:format)      children#update
                  DELETE /parents/:parent_id/children/:id(.:format)      children#destroy
          parents GET    /parents(.:format)                              parents#index
                  POST   /parents(.:format)                              parents#create
       new_parent GET    /parents/new(.:format)                          parents#new
      edit_parent GET    /parents/:id/edit(.:format)                     parents#edit
           parent GET    /parents/:id(.:format)                          parents#show
                  PUT    /parents/:id(.:format)                          parents#update
                  DELETE /parents/:id(.:format)                          parents#destroy
```

### Adding test data via Rails console

```
$ rails c

> dad = Parent.new(name: 'Paul')
 => #<Parent id: nil, name: "Paul", created_at: nil, updated_at: nil> 

> dad.save
   (0.1ms)  begin transaction
  SQL (3.0ms)  INSERT INTO "parents" ("created_at", "name", "updated_at") VALUES (?, ?, ?)  [["created_at", Wed, 29 Jan 2014 23:37:23 UTC +00:00], ["name", "Paul"], ["updated_at", Wed, 29 Jan 2014 23:37:23 UTC +00:00]]
   (1.5ms)  commit transaction
 => true 

> son = dad.children.new(name: 'Eric')
 => #<Child id: nil, name: "Eric", parent_id: 1, created_at: nil, updated_at: nil> 

> daughter = dad.children.new(name: 'Mara')
 => #<Child id: nil, name: "Mara", parent_id: 1, created_at: nil, updated_at: nil> 

> exit
```

### Adding a private controller method to load the Parent object for each method

- file: app/controllers/children_controller.rb

```
@@ -1,4 +1,7 @@
 class ChildrenController < ApplicationController
+
+  before_filter :load_parent
+
   # GET /children
   # GET /children.json
   def index
@@ -80,4 +83,11 @@ class ChildrenController < ApplicationController
       format.json { head :no_content }
     end
   end
+
+  private
+
+    def load_parent
+      @parent = Parent.find(params[:parent_id])
+    end
+
 end
```

At this point, each controller and view for the Child class model needs to be adjusted (links, redirection, form, etc)

Method: children#index

- file: app/controllers/children_controller.rb


```
   def index
-    @children = Child.all
+    @children = @parent.children.all
```

- file: app/views/children/index.html.erb

```
-    <td><%= link_to 'Show', child %></td>
-    <td><%= link_to 'Edit', edit_child_path(child) %></td>
-    <td><%= link_to 'Destroy', child, confirm: 'Are you sure?', method: :delete %></td>
+    <td><%= link_to 'Show', parent_child_path(@parent, child) %></td>
+    <td><%= link_to 'Edit', edit_parent_child_path(@parent, child) %></td>
+    <td><%= link_to 'Destroy', [@parent, child], confirm: 'Are you sure?', method: :delete %></td>
 
-<%= link_to 'New Child', new_child_path %>
+<%= link_to 'New Child', new_parent_child_path(@parent) %>
```

Method: children#new

- file: app/controllers/children_controller.rb

```
   def new
-    @child = Child.new
+    @child = @parent.children.new
```

- file: app/views/children/_form.html.erb

```
-<%= form_for(@child) do |f| %>
+<%= form_for([@parent, @child]) do |f| %>
```

- file: app/views/children/new.html.erb

```
-<%= link_to 'Back', children_path %>
+<%= link_to 'Back', parent_children_path(@parent) %>
```

Method: children#create

- file: app/controllers/children_controller.rb

```
   def create
-    @child = Child.new(params[:child])
+    @child = @parent.children.new(params[:child])
 
     respond_to do |format|
       if @child.save
-        format.html { redirect_to @child, notice: 'Child was successfully created.' }
+        format.html { redirect_to [@parent, @child], notice: 'Child was successfully created.' }
```

Method: children#show

- file: app/controllers/children_controller.rb

```
   def show
-    @child = Child.find(params[:id])
+    @child = @parent.children.find(params[:id])
```

- file: app/views/children/show.html.erb

```
-<%= link_to 'Edit', edit_child_path(@child) %> |
-<%= link_to 'Back', children_path %>
+<%= link_to 'Edit', edit_parent_child_path(@parent, @child) %> |
+<%= link_to 'Back', parent_children_path(@parent) %>
```

Method: children#edit

- file: app/controllers/children_controller.rb

```
   def edit
-    @child = Child.find(params[:id])
+    @child = @parent.children.find(params[:id])
```

- file: app/views/children/edit.html.erb

```
-<%= link_to 'Show', @child %> |
-<%= link_to 'Back', children_path %>
+<%= link_to 'Show', parent_child_path(@parent, @child) %> |
+<%= link_to 'Back', parent_children_path(@parent) %>
```

Method: children#update

- file: app/controllers/children_controller.rb

```
   def update
-    @child = Child.find(params[:id])
+    @child = @parent.children.find(params[:id])
 
     respond_to do |format|
       if @child.update_attributes(params[:child])
-        format.html { redirect_to @child, notice: 'Child was successfully updated.' }
+        format.html { redirect_to [@parent, @child], notice: 'Child was successfully updated.' }
```

Method: children#destroy

- file: app/controllers/children_controller.rb

```
   def destroy
-    @child = Child.find(params[:id])
+    @child = @parent.children.find(params[:id])
     @child.destroy
 
     respond_to do |format|
-      format.html { redirect_to children_url }
+      format.html { redirect_to parent_children_path(@parent) }
```

Method: parents#

- file: app/views/parents/show.html.erb

```
-<%= link_to 'Back', parents_path %>
+<%= link_to 'Back', parents_path %> |
+<%= link_to 'Children', parent_children_path(@parent) %>
```

At this point, the default scaffolding's links and redirection have been updated to work with the nested routes.

- - - 

# NESTED FORMS Extra


Sometimes, we need to manage a complex relationship, or create new dependent records from inside a parent form. 

Let’s look at  our Cookbook app example: 

When we switched from a `has_and_belongs_to_many`  to a `has_many :through` relationship, we gained a new `IngredientsRecipes` class. 

This class had an attribute, "quantity", which described the amount of an ingredient the recipe needs. 

However, we were unable to set this amount from within the Recipe "create" or "update" forms.

This is where nested forms come in handy. 

Nested forms allow us to manage relationships like this from within a parent form. 

Ideally, we would like to be able to not just assign `Ingredients` to `Recipes` as we have done before, but also add quantities to the relationship model.


###Configuration

Let’s go back to our Gallery app we started building yesterday (gallery_app_end.zip, shared by Gerry on hipchat today). 

In terminal:  
`$ rake db:drop`    
`$ rake db:create`  
`$ rake db:migrate`  


We can then start setting up "nested" routes for our app. In "routes.rb":

```
GalleryApp::Application.routes.draw do
  root :to => "galleries#index"

  resources :galleries do 
    resources :paintings
  end 
end
```

- - -

Now in the "paintings_controller.rb", we need to reflect that new association:

```
  def index
    @gallery = Gallery.find params[:gallery_id]
    @paintings = @gallery.paintings

    respond_to do |format|
      format.html # index.html.erb
      format.json { render json: @paintings }
    end
  end
```

Similarly, in "views/galleries/show.html.haml":

```
  <h1>
    <%= @gallery.name %>
  </h1>
  <div id="paintings">
    <% for painting in @gallery.paintings %>
      <div class="painting">
        <%= link_to gallery_painting_path(@gallery, painting) do %>
          <%= image_tag painting.image_url(:thumb)# if painting.image? %>
        <% end %>
        <div class="name">
          <%= link_to painting.title, painting %>
        </div>
        <div class="actions">
          <%= link_to "edit", edit_gallery_painting_path(@gallery, painting) %>
          |
          <%= link_to "remove", gallery_painting_path(@gallery, painting), :confirm => 'Are you sure?', :method => :delete %>
        </div>
      </div>
    <% end %>
    <div class="clear"></div>
  </div>
  <p>
    <%= link_to "Add a Painting", new_gallery_painting_path(@gallery) %>
    |
    <%= link_to "Edit Gallery", edit_gallery_path(@gallery) %>
    |
    <%= link_to "Remove Gallery", @gallery, :confirm => 'Are you sure?', :method => :delete %>
    |
    <%= link_to "View Galleries", galleries_path %>
  </p>

```

Now into the "galleries_controller.rb", change the "new" function to:

```
  def new
    @gallery = Gallery.new
    3.times { @gallery.paintings.build }
  end
```

Then in "app/views/galleries/_form.html.haml":

```
  <%= form_for @gallery do |f| %>
    <% if @gallery.errors.any? %>
      <div id="error_explanation">
        <h2>
          <%= "#{pluralize(@gallery.errors.count, "error")} prohibited this gallery from being saved:" %>
        </h2>
        <ul>
          <% @gallery.errors.full_messages.each do |msg| %>
            <li>
              <%= msg %>
            </li>
          <% end %>
        </ul>
      </div>
    <% end %>
  <% end %>

```

```
  <div class="field">
    <%= f.label :name %>
    <%= f.text_field :name %>
  </div>
  <div class="field">
    <%= f.label :address %>
    <%= f.text_field :address %>
  </div>

```

```
  <%= f.fields_for :painting do |sub_form| %>
    <fieldset>
      <div class="field">
        <%= sub_form.label :title %>
        <%= sub_form.text_field :title %>
      </div>
    </fieldset>
  <% end %>

```

```
 <div class="actions">
    <%= f.submit 'Save' %>
 </div>

```

→ we now have the option to add a painting directly when creating/editing a gallery!

However, the form currently returns an error: "Can’t mass-assign protected attributes: painting".

- - -

We thus need to get into our "gallery.rb" model, and add "paintings_attributes" to our "attr_accessible", along with a new element, "accepts_nested_attributes_for".

In "gallery.rb":

```
class Gallery < ActiveRecord::Base
  attr_accessible :address, :name, :paintings_attributes
  has_many :paintings
  accepts_nested_attributes_for :paintings
end
```

We can now extend our "_form.html.haml":

```
  <%= form_for @gallery do |f| %>
    <% if @gallery.errors.any? %>
      <div id="error_explanation">
        <h2>
          <%= "#{pluralize(@gallery.errors.count, "error")} prohibited this gallery from being saved:" %>
        </h2>
        <ul>
          <% @gallery.errors.full_messages.each do |msg| %>
            <li>
              <%= msg %>
            </li>
          <% end %>
        </ul>
      </div>
    <% end %>
    <div class="field">
      <%= f.label :name %>
      <%= f.text_field :name %>
    </div>
    <div class="field">
      <%= f.label :address %>
      <%= f.text_field :address %>
    </div>
  <% end %>

```

```
  <%= f.fields_for :painting do |sub_form| %>
    <fieldset>
      <div class="field">
        <%= sub_form.label :title %>
        <%= sub_form.text_field :title %>
      </div>
      <div class="field">
        <%= sub_form.label :artist %>
        <%= sub_form.text_field :artist %>
      </div>
      <div class="field">
        <%= sub_form.label :description %>
        <%= sub_form.text_field :description %>
      </div>
      <div class="field">
        <%= sub_form.label :price %>
        <%= sub_form.number_field :price %>
      </div>
      <div class="field">
        <%= sub_form.label :image %>
        <%= sub_form.file_field :image  → gives us the option to upload manually. %>
      </div>
      <div class="field">
        <%= sub_form.label :remote_image_url→ gives us the option to upload from a remote url %>
        <%= sub_form.text_field :remote_image_url                         %>
      </div>
    </fieldset>
  <% end %>

```
… and that’s how we build nested forms in rails!
