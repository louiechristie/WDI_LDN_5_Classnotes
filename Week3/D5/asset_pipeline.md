# Asset Pipeline

- The Asset Pipeline is the way Rails streamlines serving assets.
  * CSS
  * JS
  * Images

<http://guides.rubyonrails.org/v3.2.16/asset_pipeline.html>

- The assets in a site rarely change, so the site can be sped up if the client has already downloaded versions of them.

- But there needs to be some way to let the client know that a server version of an asset has changed

- Also, when you might have to manage assets from several locations:
  * specific to the site
  * shared across multiple sites
  * brought in from gems, etc
  ...you want the difference to be transparent to the client, but easy for the developers to manage

- Enter the asset pipeline.
  - It compresses CSS & JS files to make them smaller, and quicker to download.
    - Even quicker if somewhere between the client and server there's caching going on.
  - It "fingerprints" the files, so that any changes cause them to be re-compressed, and the new versions to be provided in requests.

- Using the asset pipeline.
  - Any assets placed in subdirectories of "public" will ignore the pipeline, and will be served as static assets (just like in Sinatra)
  - Assets that need "preprocessing" (CoffeeScript, SCSS, SASS), need to be stored in app/assets/, lib/assets/, or vendor/assets subdirectories

- When we deploy our Rails app, we need to "compile" assets to have Rails put them in public/assets/
  `rake assets:precompile`

- The tool inside Rails that's doing all this for us is called "Sprockets"

- Look into the app/assets/stylesheets/application.css file
  - It looks like CSS comments (and the JS file looks like JS comments), but the "*=" are read as commands to Sprockets, and the CSS/JS files are applied in order (so beware of overwriting previous styles or functions)

    - require_self
      places any CSS/JS in this file in the location of the require_self call

    - require_tree
      recurses the given directory path and includes every file it finds

    - require file_name
      includes the given file - this could also require other files, so you could have a "my_favourite_plugins" file which has a dozen requirements to other files

  - The order of files listed is the order they load. If you definitely need one file loaded first put it higher in the list.
  - If a file is required more than once, it will only be included the first time.

  - Using assets
    - We're already doing it... look in the layout file:  
        `<%= stylesheet_link_tag "application" %>`  
        `<%= javascript_include_tag "application" %>`  

    
    These are two helper functions that work with the asset pipeline.

    - To use images, use the image_tag helper  
      `<%= image_tag "rails.png" %>`  
      `<%= image_tag "avatars/default.png" %>`  
      `<%= image_tag "rails.png", {height: 100, width:200, alt: 'Rails Logo', id: 'rails_logo_image', class: 'logo_image'} %>`  
      `<%= image_tag "rails.png", :size => "100x200" %>`  

    - There are two asset-path helpers worth remembering:  
      `<%= asset_path 'rails.png' %>` # returns the full path of the asset
      `<%= asset_data_uri 'rails.png' %>` # turns the asset into base64-encoded data for inline inclusion
