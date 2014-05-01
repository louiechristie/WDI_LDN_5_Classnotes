# PAGINATION

    $ git clone git@github.com:Pavling/wdi-w5d3-pagination.git
    $ cd wdi-w5d3-pagination
    $ bundle install
    $ rake db:drop
    $ rake db:setup

So far, all the apps we've developed have displayed every available records on their index pages (e.g. the cookbook app displays all the recipes on the "/recipes" path). 

The way it's been working, even if we had hundreds of them, they'd all be rendered. The only reason that this has looked okay so far is that we've only had a few records in our DB at any given time.

This approach is bad - imagine what would happen if we had thousands of recipes:

  - "Recipe.all" is going to request all records from the db, this will be slow
  - the view is going to take a long time to generate as we have to render html for each recipe
  - the html page is going to be huge and slow to download
  - the page isn't going to be very practical for users


## Basic Approach

We're going to use some ActiveRecord and SQL functionality to retrieve records in 'pages' from the DB.

We can track which page of records the user wants by using a params entry (if no page key is found, we can assume the first page is wanted)

To retrieve records in pages of 5 results per page for now:
5 results from the DB (i.e. using limit)
skipping 5 results for each page (i.e. using offset)

In our "posts_controller.rb", we can then edit our index action:

    def index
      @page = params.fetch(:page, 1).to_i
      per_page = 5
      @posts = Post.offset(@page*per_page).limit(per_page).order(:created_at)
    end

We are now getting five results on the "/recipes/page=1" page.


### Generate the pagination links

For usability, we would like to display links to each of the pages of results - or at least for next/previous and first/last...

We need to calculate how many pages we need to display our full result set
e.g. amount_of_pages = (Post.count.to_f / per_page).ceil

Then generate links in the view for:

(generate them all individually? no thanks? ... With a loop? Better...)

    <% (1..amount_of_pages).each do |page| %>
      <%
        link_text = case page
                      when 1 then 'first'
                      when amount_of_pages 'last'
                      else page
                    end
      %>
      <%= link_to link_text, recipes_path(page: amount_of_pages) %>
    <% end %>

... or we could restrict our list to pages numbers either side of the current page.. or anything else we want.


Better approach... there's a gem for that
As we've already guessed, there's ALWAYS a gem for that (or two..)



## Pagination with Kaminari

The Kaminari gem will allow us to add robust, configurable pagination to our applications very easily. Kaminari internally uses AR's limit and offset. That means, uses limit and offset cause a SQL query. So, make sure to chain your pagination query with an order scope. 

-- https://github.com/niceguyjames/paginated_blog

    "gem 'kaminari'" 

in your Gemfile and bundle install.

Generate a configuration file with 

    rails g kaminari:config

Which will generate a configuration something like this:
  
    Kaminari.configure do |config|
      config.default_per_page = 5
      # config.max_per_page = nil
      # config.window = 4
      # config.outer_window = 0
      # config.left = 0
      # config.right = 0
      # config.page_method_name = :page
      # config.param_name = :page
    end

Modify our PostsController. In the index action we no longer loop through all posts, but a subset of them. Change the index action to be: 

    @posts = Post.order(:created_at).page(params[:page])

This ensures that our posts are ordered by their created_at attribute, and only the current page posts are pulled in.

Now we will need to create a link to navigate between our paginated posts. Drop the paginate helper into the views/posts/index.html.erb, generating the navigation for pages. At the bottom of the post list include:

    <%= paginate @posts %>

Finally, adding a route for nicely-formatted URLs finishes the job. in routes.rb we will pass a block to our resources posts.

    resources :posts do
      get 'page/:page', action: :index, on: :collection
    end


### Customizing

Run the generator:

    rails g kaminari:views default

Now when we go to our views folder, there will be multiple partials. We can go into the files and customise the way our pages will look. There is a gem for this at https://github.com/matenia/bootstrap-kaminari-views.

We can add this to our gemfile: 

    gem 'bootstrap-kaminari-views'

    bundle

Now we can go back to our index and update our paginate @posts to add the twitter bootstrap theme:

    <%= paginate @posts, theme: 'twitter-bootstrap' %>
