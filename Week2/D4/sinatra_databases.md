#SINATRA & DATABASES

We are building IMDB, using Sinatra and databases. 
First, let's scaffold our application.


Scaffolding the application:

The folder structure should look like this:

```
        classwork
     L→  movies_app 
    |→  public
    |→  views 
    |         |→ layout.erb
    |         L→ index.erb
    L→  main.rb
```

In "main.rb":

```
require "sinatra"
require "sinatra/contrib/all"
require "pg"

get "/" do
  erb :index
end
```

In "layout.erb":

```
<html>
  <head>
    <title></title>
  </head>
  
  <body>

    <header>
      <h1>Movies app</h1>
    </header>

    <section>  
      <%= yield %>
    </section>

    <footer>
    </footer>

  </body>
</html>
```

Let's start the server:  
`$ ruby main.rb`

If we check in Chrome - it works!


#####Implementing the database:

Let's add a "sql" folder to our architecture, and add a "dump.sql" file in it. We will store some fake movie data in it, to build our app around. 

The folder structure should now look like this.

```
        classwork
     L→  movies_app 
    |→  public
    |→  sql
    |       L→ dump.sql 
    |→  views 
    |         |→ layout.erb
    |         L→ index.erb 
    L→  main.rb
```
 
Now, copy the below SQL and paste it in your new dump.sql file.

```
create table movies
  (
    id serial4  primary key,
    title varchar(255),
    poster text,
    year varchar(4),
    rated varchar(10),
    genre varchar(100),
    director varchar(100),
    writers varchar(200),
    actors varchar(200)
    );

```

```
INSERT INTO movies (title, year, rated, genre, director, writers, actors, poster) 
VALUES ('Rocky','1976', '4', 'Drama', 'John G. Avildsen', 'Sylvester Stallone','Sylvester Stallone, Talia Shire, Burt Young', 'http://ia.media-imdb.com/images/M/MV5BMTY5MDMzODUyOF5BMl5BanBnXkFtZTcwMTQ3NTMyNA@@._V1_SX214_.jpg');
```

```
INSERT INTO movies (title, year, rated, genre, director, writers, actors, poster) 
VALUES ('Harry Potter and the Sorcerers Stone','2001', '3', 'Adventure', 'Chris Columbus', 'J.K. Rowling','Daniel Radcliffe, Rupert Grint, Richard Harris', 'http://ia.media-imdb.com/images/M/MV5BMTYwNTM5NDkzNV5BMl5BanBnXkFtZTYwODQ4MzY5._V1_SY317_CR8,0,214,317_.jpg');
```
```
INSERT INTO movies (title, year, rated, genre, director, writers, actors, poster) 
VALUES ('The Professional','1994', '5', 'Thriller', 'Luc Besson', 'Luc Besson','Jean Reno, Gary Oldman, Natalie Portman', 'http://ia.media-imdb.com/images/M/MV5BMTgzMzg4MDkwNF5BMl5BanBnXkFtZTcwODAwNDg3OA@@._V1_SY317_CR4,0,214,317_.jpg');
```

```
INSERT INTO movies (title, year, rated, genre, director, writers, actors, poster) 
VALUES ('The Kings Speech','2010', '5', 'Biography', 'Tom Hooper', 'David Seidler','Colin Firth, Geoffrey Rush, Helena Bonham Carter', 'http://ia.media-imdb.com/images/M/MV5BMzU5MjEwMTg2Nl5BMl5BanBnXkFtZTcwNzM3MTYxNA@@._V1_SY317_CR1,0,214,317_.jpg');```
```

Now let's create our database, in terminal: 
`$ createdb movies`  
We can now add the content of our "dump.sql" file into this database.  
`$ psql -d movies -f sql/dump.sql`
 
We can check this database has been created by going into psql:

```
$ psql  
$ \c movies
$ SELECT * FROM movies;
```

See? All of our data is in there!

**NOTE**: The danger with accessing the database this way (i.e. putting user input directly into the query) is that it leaves our code open to SQL injection. This is done by including portions of SQL statements in an entry field, in an attempt to get the website to pass a rogue SQL command to the database (e.g., dump the database contents to the attacker, or drop tables).

SQL injection doesn't need to be malicious though - if your queries are vulnerable, perfectly innocent user requests could cause your site to break.

We can exit psql with:  
`$ \q`


###Connecting the database:

Let's now work on displaying this on an actual webpage.

Back into main.rb, let's integrate our database. We do this with  
  `db = PG.connect(dbname: "movies", host: "localhost")`
  
→ This allows us to point at the right database (with dbname: "movies"), and as we are hosting it locally on our machine, the right host ("localhost").

In "main.rb":

```
require "sinatra"
require "sinatra/contrib/all"
require "pg"

get "/" do
  db = PG.connect(dbname: "movies", host: "localhost")

  begin
    sql = "SELECT * FROM movies"
    @movies = db.exec(sql)
    p @movies
  ensure
    db.close
  end
  erb :index
end
```

The p `@movies` line prints some data in terminal, after we've launch the server (`$ ruby main.rb`). It shows us that we've successfully established the connection to the database. Once we've checked, we can remove the `p @movies` line.


####Polishing the interface:

Now, in "index.erb":

```

  <h3>Movies index</h3>
  <% @movies.each do |movie| %>  
      <%= movie %>
  <% end %>

```

Reminder: when we're not using the = sign in the erb tag (`<%...%>`, it means we just want to run some ruby code. It will not be displayed on the page.
The equal sign (`<%= ... %>`) will allow the result of the ruby code to be displayed on the page.

In Chrome, we can see that our movies are displayed... but not quite in a readable fashion. For now, we have a raw "movie" hash. Let's make this a little better.

In "index.erb":  

```
  <h3>Movies index</h3>
  <ul>
    <% @movies.each do |movie| %>  
        <li><h4><%= "#{movie["title"]}  (#{movie["year"])" %></h4></li>
    <% end %>
  </ul>
```

This syntax allows us, for each movie in the movie hash, to go fetch the value associated with the "title" key, the value associated with the "year" key, and to display the results as a list.
Now in Chrome, we can see that our movies as displayed in a much more readable way.

Let's add links (`<a href="/movies/<%=movie["id"]%>">`) to this list. In order to make our code more manageable, let's reindent properly.

In "index.erb":

````
 <h3>Movies index</h3>
 <ul>
  <% @movies.each do |movie| %>  
   <li>
     <h4>
       <a href="/movies/<%=movie["id"]%>">
         <%= "#{movie["title"]} (#{movie["year"])" %>
       </a>
     </h4>
   </li>
  <% end %>
 </ul>

````

If we test this out in Chrome however, if our movies have become clickable, they still don't direct to any page: we haven't created a movie page yet.

Back to our "main.rb" file:


```
require "sinatra"
require "sinatra/contrib/all"
require "pg"
```
```
get "/" do
  db = PG.connect(dbname: "movies", host: "localhost")
  begin
    sql = "SELECT * FROM movies"
    @movies = db.exec(sql)
  ensure
    db.close
  end
  erb :index
end
```
```
get "/movies/:movie_id" do
  db = PG.connect(dbname: "movies", host: "localhost")
  begin
    movie_id = params[:movie_id].to_i
    sql = "SELECT * FROM movies WHERE id=#{movie_id}"
    @movie = db.exec(sql).first
  ensure
    db.close
  end
  erb :show
end
```

We now have a way to display specific information on movies pages. Each movie link could now be redirecting to a page that display all the data specific to that movie. But we're not done yet.

Since we've added  `erb :show` , we need to create a new view.

Let's create a "show.erb" page.

The folder structure should now look like this.

```
        classwork
     L→  movies_app 
    |→  public
    |→  sql
    |       L→ dump.sql 
    |→  views 
    |         |→ layout.erb
    |         |→ index.erb
    |         L→ show.edb
    L→  main.rb
```

In "layout.erb", let's transform our header title ("Movies app") into a link redirecting to our homepage:

```
<html>
  <head>
    <title></title>
  </head>
  <body>

    <header>
      <a href="/">
        <h1>Movies app</h1>
      </a>
    </header>

    <section>  
      <%= yield %>
    </section>
    <footer>
    </footer>
  </body>
</html>
```

In our new "show.erb" file:


```
  <h3><%= @movie["title"] %></h3>

  <img src="<%= @movie["poster"] %>

  <p>Year: <%= @movie["year"] %></p>
  <p>Rated: <%= @movie["rated"] %></p>
  <p>Actors: <%= @movie["actors"] %></p>
  <p>Genre: <%= @movie["genre"] %></p>

```

We now have all it takes to display quite a bit of data on our show page. Let's test it out in Chrome. 


⇒ We've just built IMDB!


####Implementing search functionality

If we do want to compete seriously with IMDB though, we might need some more functionality. Let's add search functionality to our app.

In "layout.erb", let's add a form:

```
<html>
  <head>
    <title></title>
  </head>
  <body>

    <header>
      <a href="/">
        <h1>Movies app</h1>
      </a>
      <form method="post" action="/search">
        <input type="text" name="query"/>
        <input type="submit"/>
      </form>
    </header>

    <section>  
      <%= yield %>
    </section>
    <footer>
    </footer>
  </body>
</html>
```

Reloading in Chrome, we can see that the seach box now exists - but it doesn't work yet.

Let's add a new method in our "main.rb" file. After the code we already have, add:

```
post "/search" do
  db = PG.connect(dbname: "movies", host: "localhost")
  begin
    query_string = params[:query]
    sql = "SELECT * FROM movies WHERE title LIKE '%#{query_string}%'"
    @movies = db.exec(sql)
  ensure
    db.close
  end
  erb :index
end
```

Notice the `erb :index`. We don't need to create another template - that is why we reuse "index.erb" to show the search results.

Let's try out our search functionality in Chrome. Careful, it's case-sensitive!
"Harry" [Submit] ---> returns the Harry Potter movie entry! 

If you want to make NOT case-sensitive, we can use `ILIKE` (which stands for insensitive-like):
`sql = "SELECT * FROM movies WHERE title ILIKE '%#{query_string}%'"`


###More functionality: adding a new movie to our database

In "main.rb", add:

```
get "/new" do
  erb :new
end
```

Let's add a "new.erb" view to our file structure.
The folder structure should now look like this:

```
        classwork
     L→  movies_app 
    |→  public
    |→  sql
    |       L→ dump.sql 
    |→  views 
    |         |→ layout.erb
    |         |→ index.erb
    |         |→ new.erb 
    |         L→ show.edb
    L→  main.rb
```

In our "new.erb" file:


```
 <h3>Add a new Movie</h3>
 <form method="post" action="/new">
  Title: <input type="text" name="title"/>
  Year: <input type="number" name="year"/>
  Rated: <input type="number" name="rated"/>
  Poster: <input type="text" name="poster"/>
  Director: <input type="text" name="director"/>
  Actors: <input type="text" name="actors"/>
  <input type="submit">
 </form>
```

In "main.rb", let's now build this new bit of functionality. Add:

```
post "/new" do
  db = PG.connect(dbname: "movies", host: "localhost")
  begin
    sql = "INSERT INTO movies (title, year, rated, poster, director, actors) VALUES ('#{params[:title]}', '#{params[:year]}', '#{params[:rated]}', '#{params[:poster]}', '#{params[:director]}', '#{params[:actors]}') RETURNING id"
    @movies = db.exec(sql)
    new_created_id = @movie[0]["id"]
    redirect "/movies/#{new_created_id}
  ensure
    db.close
  end
  erb :new
end
```

We are now able to create movies and add them to our database.

####A few words on Get/Post/Put/Delete:

So far, we've seen:

* Create
* Read
* Update
* Delete
⇒ CRUD ⇐=> REST (Representational State Transfer). 

It uses common HTTP verbs that we've seen extensively in our app today, such as Get (i.e. Read), Post (i.e Create), Delete (i.e. Delete), and Put (i.e. Update)

After a quick glance at this list, what are we missing so far in our Movie app? "Delete" and "Update". Let's build this functionality into our app.


####Deleting entries from our database:

In "main.rb", add:

```
post "/movies/:movie_id/delete" do
  db = PG.connect(dbname: "movies", host: "localhost")
  begin
    movie_id = params[:movie_id]
    sql = "DELETE FROM movies WHERE id=#{movie_id}"
    db.exec(sql)
  ensure
    db.close
  end
  redirect "/"
end
``` 

In our "show.erb" form, we can add the delete functionality. At the bottom, let's add a DELETE form:

```
<h3><%= @movie["title"] %></h3>
  <img src="<%= @movie["poster"] %>
  <p>Year: <%= @movie["year"] %></p>
  <p>Rated: <%= @movie["rated"] %></p>
  <p>Actors: <%= @movie["actors"] %></p>
  <p>Genre: <%= @movie["genre"] %></p>

<form method="POST" action"/movies/<%= @movie["id"]%>/delete">
  <input type="submit" value="Delete">
</form>
```

So we can now erase entries from our database. We're just missing the "Update" functionality.


####Updating entries in our database:

In "main.rb", we can write the UPDATE functionality. Let's add:

```
get "/movies/:movie_id/update" do
  db = PG.connect(dbname: "movies", host: "localhost")
  begin
    movie_id = params[:movie_id]
    sql = "SELECT * FROM movies WHERE id=#{movie_id}"
    @movie = db.exec(sql).first
  ensure
    db.close
  end
  erb :new
end
```

We are reusing the "new.erb" template, as it allows us to directly target the elements we want to update.

For this to work though, let's do a bit of work in our "new.erb" file:

```
<h3>Add a new Movie</h3>
<% if @movie["id"]%>
  <form method="post" action="/movies/<%=@movie["id"]%>/udpate">
<% else%>
  <form method="post" action="/new">
<% end %>

 Title: <input type="text" name="title" value="<%= @movie["title"]%> />

  Year: <input type="number" name="year" value="<%= @movie["year"]%> />

  Rated: <input type="number" name="rated" value="<%= @movie["rated"]%> />

  Poster: <input type="text" name="poster" value="<%= @movie["poster"]%> />

  Director: <input type="text" name="director" value="<%= @movie["title"]%> />

  Actors: <input type="text" name="actors" value="<%= @movie["actors"]%> />

  <input type="submit">
</form>
```

As we can see, we've added a "value" to each of our entries, as well as an "if/else" statement that allows us to detect whether we are creating a new entry or just updating an existing one (does this movie have an ID? No → it's new. Yes → let's update it).

Let's look at the result in Chrome. Now, when selecting an existing movie, its various content fields are editable!

We need to ensure we add this "Update" functionality to our "main.rb" file:

```
post "/movies/:movie_id/update" do
  db = PG.connect(dbname: "movies", host: "localhost")
  begin
   movie_id = params[:movie_id]
   sql = "UPDATE movies SET
    title = '#{params[:title]}', 
    year = '#{params[:year]}',
    rated = '#{params[:rated]}',
    poster = '#{params[:poster]}', 
    director = '#{params[:director]}', 
    actors = '#{params[:actors]}' WHERE id=#{movie_id}"
   db.exec(sql).first
  ensure
    db.close
  end
  redirect "/movies/#{movies_id}"
end
```

Let's add a final touch to our "show.erb" file - link to the "Update" page:

`<a href="/movies/<%=@movie["id"]%>/update"> Update <%= @movie["title"]%></a>`

It now works properly. We've created a fully functioning CRUD application!


Sinatra & Databases:  
<http://en.wikipedia.org/wiki/Create,_read,_update_and_delete>  
<http://programmers.stackexchange.com/questions/120716/difference-between-rest-and-crud>

