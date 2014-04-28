# File Uploads/Carrier Wave

Carrier Wave is a gem provides a simple and extremely flexible way to upload files from Ruby applications. It works well with Rack based web applications, such as Ruby on Rails.
Wiki page available here.

```
rails new carrierwave
cd carrierwave
rails g scaffold Recipe name
rake db:migrate
rails s
```


We will need to add the gem to our gemfile.

`gem 'carrierwave'`

For rails 3.2
`gem 'carrierwave', '= 0.9.0'`

Now we will need to run bundle:

`bundle`

Now we will generate an uploader.

`rails g uploader RecipeImage`

This will create a file for us at: `app/uploaders/recipe_image_uploader.rb`

When you run your app, you will likely serve your assets from a cdn. A content delivery network or content distribution network (CDN) is a large distributed system of servers deployed in multiple data centers across the Internet. 

We will need some way of linking the image we upload to the space in our database. We will generate a migration for this. 

`rails g migration AddRecipeImageToRecipes recipe_image:string`

This will create a migration which will add a RecipeImage column to our Recipes table.
Now migrate your database.

`rake db:migrate`

Now we will mount the uploader to our recipe. In the recipe model add the uploaded definition and the accessible attribute:

```
class Recipe < ActiveRecord::Base
  attr_accessible :recipe_image
  mount_uploader :recipe_image, RecipeImageUploader
end
```

Here we are specifying the table column followed by the uploader class.

Now if we go into view, and recipes form, we can add:


```
  <div class="field">
    <%= f.label :recipe_image %><br />
    <%= f.file_field :recipe_image %>
  </div>
```

In the recipe/show page and add the new list item. 

```
  <div>
    <%= image_tag @recipe.recipe_image.url %>
  </div>
```

We will now be able to upload your images through the new recipe form. These images will be stored at: public/uploads/recipe/recipe_image/
In the db it will be the filename.

Style it with CSS....

```
img {
  width: 100px;
  height: 100px;
  border: 1px solid #6B1229;
}
```

In order to manipulate our images, we will need to use the gem 'rmagick'

`gem 'rmagick'`

We will first need to install imagemagick:

`brew install imagemagick`

And then bundle:

`bundle`

(if this breaks with an error finding 'MagickWand.h' then `brew install pkg-config` and bundle again)

Now we can go into our recipe_image_uploader.rb and define some versions. 

We can first uncomment the line:

`include CarrierWave::RMagick`

Now we can manipulate images. Now uncomment the lines:

```
version :thumb do
  process :scale => [50, 50]
end
```

And change it to: 

```
version :thumb do
  process :resize_to_fill => [100, 100]
end
```

Now add these lines below your thumb version:

```
version :detail do
  process :resize_to_fit => [400, 400]
end
```

We will now need to remove the css on our images, to allow rmagick to do the work. Remove the styling from your css.

Now we can go back into our recipe/show view and update this line:

`<%= link_to image_tag(@recipe.recipe_image.thumb.url), @recipe.recipe_image.detail.url %>`

This will render the thumbnail, and link to the 'detail' version we specified in recipe_image_uploader.rb.
