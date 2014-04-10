# Homework

The aim of this homework is to expand the capabilities of the movie app we built in class. In addition to storing movies in the DB, we now also want to store actors.

Your mission, should you choose to accept it, is to add an "actors" table to the DB. We want users to be able to perform the same CRUD actions (Create, Read, Update, Delete) that they can already perform for "movies". 

More specifically, the users should be able to:

- list all actors in the DB
- filter the list by entering a search term
- show detailed info for a particular actor
- create a new actor in the DB
- delete an actor from the DB

Information we want to store about actors includes:

- first_name
- last_name
- dob (date of birth)
- image_url (a link to an image of the actor)


## Context

Once we can perform CRUD for both "actors" and "movies", we'll be ready to explore how we can use DB records in combination with each other. We'll explore this later in class, but have a think about how this might work in practice. 

For  instance, it would be great if we can associate actors with the movies they've performed in and then let users see the following:

- all the movies an actor has starred in
- all the actors who have starred in a particular movie
