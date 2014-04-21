# Custom Helpers

ApplicationHelper is used to store global helpers. Helpers are just a module with methods that get mixed into our views to move logic away from our templates, such as calculating costs in pounds and dollars, then displaying the results.

Controller-specific helpers can be stored in their equivalent files, i.e. IngredientsHelper or RecipesHelper. Helpers belong in app/helpers. If a helper didn't get created when you generated a controller, just touch app/helpers/controllername_helper.rb, where controllername is the snake-cased controller name.

By including methods in ApplicationHelper, we can ensure they are available to all our views. By doing this, we are stating that a helper is 'global'.

```
module ApplicationHelper
  def total_cost(recipe)
    prices = recipe.ingredients.map { |ingredient| ingredient.cost.to_f }
    dollar_total = prices.inject(:+)
    pound_total = (dollar_total / 1.49).round(2)
    "$#{dollar_total} or £#{pound_total}"
  end
end
```

We can then call this helper anywhere in our views with `<%= total_cost(@recipe) %>` (where @recipe is an object containing an array of ingredients).

Any methods we put in a controller-specific helper file will only be available in a view rendered by that controller. Therefore, we are namespacing any methods contained within.

```
module IngredientsHelper
  def cost_in_pounds(cost)
    "£#{(cost / 1.49)}"
  end
end
```

We can then call this helper anywhere in a view rendered by the IngredientsController. `<%= cost_in_pounds(100) %>`, for example. Using it outside a view rendered by the IngredientsController will result in an error.


## Using helpers in controllers
Sometimes, we want to define a method that can be used in both the view and the controller. 

We define these methods in the controller, and append the method helper_method. This takes a symbol representation of the method we want to share in the controller and the view.

```
helper_method :say_hello
def say_hello(name)
  "Hello #{name}!"
end
```