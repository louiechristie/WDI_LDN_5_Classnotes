# Validations
Validations are used to ensure that only valid data is saved into your database. For example, it may be important to your application to ensure that every user provides a valid email address and mailing address. 

Model-level validations are the best way to ensure that only valid data is saved into your database. They are database agnostic, cannot be bypassed by end users, and are convenient to test and maintain. 

Rails makes them easy to use, provides built-in helpers for common needs, and allows you to create your own validation methods as well.

How does input validation work:   

1. user fills in a form
2. data is sent to the server
3. verification: is the data valid?
  - yes: process the data
  - no: display the form again

Validations are run before a record is saved. If any validations fail, the object does not get saved to the database.

## Some validations:  

    * presence
      - validates :name, presence: true

    * length
       - validates :name, length: { minimum: 2 } 
       - validates :name, length: { maximum: 500 }
       - validates :name, length: { in: 8..255 }
 
    * numericality
      - validates :value, numericality: true
      - validates :value, numericality: { only_integer: true }
        - also allows for :greater_than, :less_than, :odd, :even, :equal, et al.

    * uniqeness
      - validates :name, uniqueness: true

In our "ingredient.rb" model, we can add our validation. For a start, let's ensure that our ingredient is created with a name:

```
class Ingredient < ActiveRecord::Base
  attr_accessible :name, :image
  has_many :quantities
  has_many :recipes, through: :quantities

  validates :name, presence: true

end
```

Some Activerecord methods skip validations, but "create", "save", and "update_attributes" (and their bang(!) alternatives) require validations to pass.

We can test that in the console:  

`$ rails c`  
`$ ingredient = Ingredient.new`  
`$ ingredient.name = "snails"`  
`#=> "snails"`  
`$ ingredient.valid?`  
`#=> true`  
→ since we've added a name (at least one character), the ingredient's name is present - it has been validated.

`$ ingredient.name = nil`  
`#=> nil`  
`$ ingredient.valid?`  
`#=> false`  
→ without a name, the ingredient cannot be validated. 

Let's add a validation for "image":  

```
class Ingredient < ActiveRecord::Base
  attr_accessible :name, :image
  has_many :quantities
  has_many :recipes, through: :quantities

  validates :name, presence: true
  validates :image, presence: true
end
```

Let's test this in the console again:  
`$ reload! `  
`$ ingredient = Ingredient.new`  
`$ ingredient.valid? `  
`#=> false`  → we're missing both an image and a name  
`$ ingredient.name = "Something"`  
`$ ingredient.valid?`  
`#=> false ` → we're still missing an image  
`$ ingredient.image = "Something"`  
`$ ingredient.valid? `  
`#=> true`

- - -

There are many parameters we can validate, e.g.: length, inclusion, presence, etc...

Let's look at some of these by further customizing our validations:  

```
class Ingredient < ActiveRecord::Base
  attr_accessible :name, :image
  has_many :quantities
  has_many :recipes, through: :quantities

  validates :name, presence: true, uniqueness: true
  validates :image, presence: true
end
```

→ "uniqueness: true" ensures that we're not creating duplicated entries in our database.

We can test this in the console by creating and trying to save two ingredients with the same name.

- - - 

We can also build validations directly into our controllers.

Let's update our "ingredients_controller.rb":

```
class RecipesController < ApplicationController
  def index
    @ingredients = Ingredient.all
  end
  def show
    @ingredient = Ingredient.find params[:id]
  end
  def new
    @ingredient = Ingredient.new
  end
  def create
    ingredient = Ingredient.create params[:ingredient]
    if @ingredient.save
      redirect_to @ingredient
    else
      render :new
    end
  end
  def edit    
    @ingredient = Ingredient.find params[:id]
  end
  def update
    ingredient = Ingredient.find params[:id]
    ingredient.update_attributes params[:ingredient]
    redirect_to ingredient
  end
  def destroy
    ingredient = Ingredient.find params[:id]
    ingredient.delete
    redirect_to ingredients_path 
  end
end
```

→ look at the "create" method. We're ensuring that the ingredient has been created successfully. If that's not the case, we're displaying the "new" template again.

Now back into our ingredient "_form.html.haml" partial, let's add a new functionality: an error message. 

In "_form.html.haml":  

```
<%= @ingredient.errors.full_messages %>
<%= form_for(@ingredient) do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>
  <%= f.label :image %>
  <%= f.text_field :image %>
  <%= f.submit %>
<% end %>
```

→ by calling ".errors.full_messages", we get an array containing all the errors appearing on our page.  
But it isn't a very nice way to present these errors. Let's reformat it:  

```
  <ul class="errors">
    <% @ingredient.errors.full_messages.each do |error| %>
      <li>
        <%= error %>
      </li> 
    <% end %>
  </ul>
  <%= form_for(@ingredient) do |f| %>
    <%= f.label :name %>
    <%= f.text_field :name %>
    <%= f.label :image %>
    <%= f.text_field :image %>
    <%= f.submit %>
  <% end %>
```

Since we've added a ".errors" class to our %ul, we can now apply some styling to it. 
In the "app/assets/stylesheets" folder, let's create a "style.css.scss" file:  

```
.errors {
  background-color: red;
}
```

In only five lines of code (including our stylesheet), we've created a working validation. We will discover many more ways to validate user input over the next few weeks.

- - -

Let's add some validation to our Quantity model.

In "quantity.rb":  

```
class Quantity < ActiveRecord::Base
  attr_accessible :ingredient_id, :description, :quantity, :measurement, :price
  belongs_to :recipe
  belongs_to :ingredient

  validates :ingredient_id, presence: true
  validates :recipe_id, presence: true
  validates :quantity, numericality: true
  validates :price,  numericality: true
end
```

→ not only are we making sure that "ingredient_id" and "recipe_id" are present, we're also ensuring that both the "price" and "quantity" data provided by the user in the form are indeed numbers.

In our "quantities_controller.rb":  

```
class QuantitiesController < ApplicationController
  def new
    @recipe = Recipe.find params[:recipe_id]
    @quantity = @recipe.quantities.new
  end

  def create
    @recipe = Recipe.find(params[:recipe_id])
    @recipe.quantities.create params[:quantity]
    if @quantity.save
      redirect_to @recipe
    else
      render.new
    end
  end
end
```

Let's now add the code that will display the error message in our form.

In "_form.html.haml":

```
  <ul class="errors">
    <% @quantity.errors.full_messages.each do |error| %>
      <li>
        <%= error %>
      </li>
    <% end %>
  </ul>
  <%= form_for [@recipe, @quantity] do |f| %>
    <%= f.label :ingredient_id, "ingredient" %>
    <%= f.select :ingredient_id, options_from_collection_for_select(Ingredient.all, 'id', 'name') %>
    <br/>
    <%= f.label :description %>
    <%= f.text_field :description %>
    <br/>
    <%= f.label :quantity %>
    <%= f.number_field :quantity %>
    <br/>
    <%= f.label :measurement %>
    <%= f.text_field :measurement %>
    <br/>
    <%= f.label :price %>
    <%= f.number_field :price %>
    <br/>
    <%= f.submit %>
  <% end %>
```

- - -

##Passing arguments to our validation

We can customize the type of validation we apply to our fields by passing arguments to it.   
For example, let's look at "numericality" (in our Quantity model).

```
In "quantity.rb":
class Quantity < ActiveRecord::Base
  attr_accessible :ingredient_id, :description, :quantity, :measurement, :price
  belongs_to :recipe
  belongs_to :ingredient

  validates :ingredient_id, presence: true
  validates :recipe_id, presence: true
  validates :quantity, numericality: {greater_than: 0}
  validates :price, numericality: {greater_than_or_equal_to: 0, less_than_or_equal_to: 100}

end
```

##Conditional validation

Sometimes we only want to validate if certain conditions are met. 
We can do this using "on", "if" and "unless". 

By using "on", we are able to specify when the validation should take place:

In "quantity.rb":

```
class Quantity < ActiveRecord::Base
  attr_accessible :ingredient_id, :description, :quantity, :measurement, :price
  belongs_to :recipe
  belongs_to :ingredient

  validates :ingredient_id, presence: true, on: :update
  validates :recipe_id, presence: true
  validates :quantity, numericality: {greater_than: 0}
  validates :price, numericality: {greater_than_or_equal_to: 0, less_than_or_equal_to: 100}

end
```

##Custom validation

We can write our own methods to verify the state of our objects.

We can tell call them by using "validate :my_custom_validation_method". 

Note that "validate" doesn't take a "S" for custom validators.

In "quantity.rb":  

```
class Quantity < ActiveRecord::Base
  attr_accessible :ingredient_id, :description, :quantity, :measurement, :price
  belongs_to :recipe
  belongs_to :ingredient

  validates :recipe_id, presence: true
  validates :quantity, numericality: {greater_than: 0}
  validates :price, numericality: {greater_than_or_equal_to: 0, less_than_or_equal_to: 100}

  validate :my_own_validator

  def my_own_validator
    if Ingredient.count > 0 && self.ingredient_id.present?
      errors.add(:ingredient_id, "must have a value")
    end
  end

end
```

We can also do custom validations this way:

```
class Quantity < ActiveRecord::Base
  attr_accessible :ingredient_id, :description, :quantity, :measurement, :price
  belongs_to :recipe
  belongs_to :ingredient

  validates :ingredient_id, presence: true, if: ingredient_exists?
  validates :recipe_id, presence: true
  validates :quantity, numericality: {greater_than: 0}
  validates :price, numericality: {greater_than_or_equal_to: 0, less_than_or_equal_to: 100}

  def ingredient_exists?
    ingredient.count > 0
  end
end
```

- - - 

What if we want an ingredient's quantity to be unique within the scope of a recipe?

In "quantity.rb":

```
class Quantity < ActiveRecord::Base
  attr_accessible :ingredient_id, :description, :quantity, :measurement, :price
  belongs_to :recipe
  belongs_to :ingredient

  validates :ingredient_id, presence: true, if: ingredient_exists?
  validates :ingredient_id, uniqueness: {scope: [:recipe_id], case_sensitive: false}
  validates :recipe_id, presence: true
  validates :quantity, numericality: {greater_than: 0}
  validates :price, numericality: {greater_than_or_equal_to: 0, less_than_or_equal_to: 100}

  def ingredient_exists?
    ingredient.count > 0
  end
end
```

→ this way, we can store more than one time the same quantity of ingredients within our quantity table. But we CAN'T have it more than once within one particular recipe.

## More notes on validation. 

### Conditionally validating

- Sometimes we only want to validate if certain conditions are met: 

## :if and :unless

### with symbol for method 

```
class Order < ActiveRecord::Base
  validates :card_number, presence: true, if: :paid_with_card?
 
  def paid_with_card?
    payment_type == "card"
  end
end
```

### with Proc

```
class User < ActiveRecord::Base
  validates :password, confirmation: true, unless: Proc.new { |u|u.password.blank? }
  validates :password, confirmation: true, unless: -> (u) { u.password.blank? }
end
```

## :on

Lets you specify when the validation should take place - create/update/save (defaults to 'save')

```
class Person < ActiveRecord::Base
  # it will be possible to update email with a duplicated value
  validates :email, uniqueness: true, on: :create
 
  # it will be possible to create the record with a non-numerical age
  validates :age, numericality: true, on: :update
 
  # the default (validates on both create and update)
  validates :name, presence: true, on: :save
end
```

#ADDITIONAL RESOURCES

Validations:  
<http://guides.rubyonrails.org/v3.2.17/active_record_validations_callbacks.html>  
<http://edgeguides.rubyonrails.org/active_record_validations.html>