# Rendering in Rails

- A few ways that controllers can control what the view does (or doesn't do)

```
  $ rails new rendering
  $ cd rendering
  $ rails g controller products index show special_show
  edit routes
```

  - By default, Rails will render the view that matches the action name, from the folder that matches the controller's name

  - To render nothing:  
  `render nothing: true`

  - To render a specific action's view (so from a search action you could render):  
  `render 'list'`  
  `render :list `   
  `render action: :list `   
  ### note: none of those run any of the referenced action's code... just its view file

  - To render a specific action's view in another controller:  
  `render 'products/show'`

  - To render an arbitrary file (takes an explicit file-system path):  
  `render '/Users/michael/test_file'`  
  `render file: '/Users/michael/test_file'`

  - To render specific text:  
  `render text: "OK"`  

  - To render JSON or XML (Rails will expect the object you pass in to have a ".to_json" or ".to_xml" method):  
  `render json: @product`  
  `render xml: @product`  

  - To override the default layout:  
    `render layout: 'alternative_layout'` # expects there to be an 'alternative_layout.html.erb' file in the views/layouts/ directory  
    `render layout: false`

    There are also ways to specify the layout per controller, and even by each request.


  - DoubleRenderError
    - You will see this error from time to time. Here is some code that would cause it:
  
  ```
      def show
        @book = Book.find(params[:id])
        if @book.special?
          render :action => "special_show"
        end
        render :action => "regular_show"
      end
  ```
  
      The convention is to add "and return"  
        `render :action => "special_show" and return`


  - Redirecting rather than rendering.
    - Redirecting tells the browser to make a new request for the redirected path.
      Note: You can only redirect to GET paths... this is a bit of an obsticle sometimes
      Also, the redirect requires a round-trip to the browser, so can slow down response times.
  
    `redirect_to photos_url`  
    `redirect_to @product`  
    `redirect_to 'http://google.co.uk'`  

  - An alternative:  
    <http://cells.rubyforge.org/>


    <http://guides.rubyonrails.org/v3.2.17/layouts_and_rendering.html>
