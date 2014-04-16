# Planets App Code-Along

  - Create an app to store astronomical information about the planets 
  - Each planet has a name, an image, an orbit (how far it is from the sun), a diameter, a mass, and a number of moons. (Note: 1 AU is the distance between Earth and the Sun.) This sounds a bit like some of the object-oriented programming we did last week.

`  rails new planets -d postgresql`

 - check the gemfile and the database.yml
 - remove `username: planets` &   `password:` from database.yml
 - add `gem 'pry-rails'` to your gemfile
 
In SQLite, Rails will create the database file for us, but in Postgres we have to create the DB - but Rails gives us a command (which will create a DB whatever the engine we use - so we only need to remember one command)


In terminal

`rake db:create`

`rails g model Planet name:string image:text orbit:float diameter:float mass:float moons:integer`

  - edit the migration and add "limit: 2" to the moons field definition
  
  
`rake db:migrate`


- look at the planet.rb model in editor
    - note the "attr_accessible"
    
Back in terminal 


`p = Planet.create(name: 'Earth', moons: 1, orbit: 1)`  
`p2 = Planet.create(name: 'Mars', moons: 2, orbit: 1.5)`  
`p3 = Planet.create(name: 'Jupiter', moons: 67, orbit: 5.2) `   
    
`Planet.all`

### In DB seeds.rb

`Planet.delete_all`

then in terminal 

`rake db:seed`


### Now in routes.rb add

`get '/planets' => 'planets#index'`


### Terminal

`touch app/controllers/planets_controller.rb`

### Now go to controllers/planets_controller.rb in SUBL

```

    class PlanetsController < ApplicationController
      def index
      end
    end
```

###Terminal

  `mkdir app/views/planets`  
  `touch app/views/planets/index.html.erb`

 
### In index action (your planets controller)
   `@planets = Planet.all `

### In index view (views / planets)

```
<% @planets.each do |planet| % >
  <%= planet.name %> has <%= pluralize(planet.moons, 'moon') %> .
  <br >
<% end %> 
```



### in layouts/application.html.erb

```
 <nav >
   <ul >
     <li><%= link_to('Planets', planets_path) %></li >
     <li><%= link_to('New Planet', planets_new_path) %></li >
   </ul >
 </nav> 
```


### routes & add 'new' action to PlanetsController

- in routes 


`get '/planets/new' => 'planets#new' `

- terminal

`touch app/views/planets/new.html.erb `

`rake routes`


### In planets/new.html.erb
```
    <form method='post' action='/planets'>
      Name:
      <input type='text' name='name' autofocus>
      <br>
      Image:
      <input type='text' name='image'>
      <br>
      Orbit:
      <input type='text' name='orbit'>
      <br>
      Diameter:
      <input type='text' name='diameter'>
      <br>
      Mass:
      <input type='text' name='mass'>
      <br>
      Moons:
      <input type='text' name='moons'>
      <br>
      <button>planet me!</button>
    </form>
```

### In routes

`post '/planets' => 'planets#create'`

### PlanetsController

```
def create
  render text: 'test'
end
```

#### Now replace in PlanetsController create action

`Planet.create(params[:planet])`  
`redirect_to(planets_path)`



### Back in routes.rb

`get '/planets/:id' => 'planets#show'

`touch app/views/planets/show.html.erb`

### PlanetsController

```
def show
  render text: 'show planet'
end
```

#### Now in terminal 

`rake routes`


## show doesn't have a name !!

update your routes to

`get '/planets/:id' => 'planets#show', as: 'planet'`


#### go to the Rails console

`  app.planet_path`

`  app.planet_path(3)`



### In index.html.erb

`<%= link_to(planet.name, planet_path(planet.id)) %>`

### PlanetsController

`@planet = Planet.find(params[:id])`

### show.html.erb

`<%= image_tag(@planet.image) %>`




# Deleting planets (Death Star method?)

### routes  
`post '/planets/:id/delete' => 'planets#destroy', as: 'planet_delete'`

### index view
`<%= button_to('Delete Planet', planet_delete_path(planet.id)) %>`

### controller

```
def destroy
  planet = Planet.find(params[:id])
  planet.destroy
  redirect_to(planets_path)
end    
```

# Editing planets (terraforming?)

### routes
`get '/planets/:id/edit' => 'planets#edit', as: 'planet_edit'`

### controller

```
def edit
  @planet = Planet.find(params[:id])
end
```

### edit view

`cp app/views/planets/new.html.erb app/views/planets/edit.html.erb`

### index page  

`<%= link_to('Edit Planet', planet_edit_path(planet.id)) %>`


### edit view

#####edit the form and fields...
  `<form method='post' action='<%= planet_path(@planet.id) %>'>`

  `<input value='<%= @planet.name %>' type='text' name='planet[name]'>`


### routes

#### need an update route for the form
in routes

 ` post 'planets/:id' => 'planets#update'`

#### and an update action in the controller

```
  def update
    planet = Planet.find(params[:id])
    planet.update_attributes(params[:planet])
    redirect_to(planets_path)
  end
```
