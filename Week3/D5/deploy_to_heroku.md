#Heroku

  Heroku is a cloud platform service. Essentially it's virtual hosting for your apps. And it's free - as in beer



### IMPORTANT ###


We will need to make sure that the app we are deploying is not using a sqlite database, as heroku does not support this database.
  
##SSH

As we did for github, we need to link our machine with heroku. The first thing we do to set up heroku is supply our SSH key. 

  - ###add your ssh-key to your Heroku account.
  
  To get your ssh key, go into the command line and type:

     `# cat ~/.ssh/id_rsa.pub| pbcopy`
    
  Copy the key that is returned, goto [Heroku](https://www.heroku.com/)/account/SSH Keys. Paste this key into the textfield, and click “add”.
  
## Heroku Toolbelt

- ###install toolbelt https://toolbelt.heroku.com/

 
We will now need to install the heroku toolbelt.  
<https://toolbelt.heroku.com/osx> 

`heroku login`  

`rails new deploy_test -d postgresql`  

`cd deploy_test `   

`echo ruby-2.0.0 > .ruby_version`  


    
- #### add "ruby '2.0.0'" to Gemfile

`echo ruby-2.0.0 > .ruby_version`


- #### edit database.yml and remove username/password

`RAILS_ENV=production bundle exec rake db:create`

`RAILS_ENV=production bundle exec rake assets:precompile`

- #### config.assets.initialize_on_precompile = false

`rails g scaffold Customer name:string`  

`heroku create`

`git push heroku master`    

`heroku run rake db:migrate`

`heroku open`

    
   
# MORE on heroku .... / Other   
   
    
##Deploying a new project

On the command line, move into the directory of the Rails application which we want to deploy.

`git init`

`git add .`

`git commit -m "initial commit"`


Now, let’s open the app in Sublime, go to “config/application.rb”, add:

`config.assets.initialize_on_precompile = false`


Again:  
`$ git add .`
`$ git commit -m "changed assets precompile config"`  


Now we can run heroku create and submit a name for our app:  
`$ heroku create gallery-app-first-name`  
(should you decide to just run “heroku create”, heroku will feel creative and assign a random name to your app (“creepy_journey” or “misty_depths”, for instance. Seriously.).


Then we can push to heroku!  
`$ git push heroku master`

Now to view our webpage in the browser we can run.  
`$ heroku open`  

IT DOESNT WORK! Why? 

Because we probably need to migrate our database on heroku:  
`$ heroku run rake db:migrate`

Then:  
`$ heroku run rake db:seed`

And good to know, we can also run a console for our heroku app by typing:   
`$ heroku run rails console `  
→ we can use this exactly the same as irb.

Now to view our webpage in the browser we can run.
`$ heroku open`  
 
AND IT WORKS! 


# For when we are using AWS

- This process is the same when we create apps that rely on access keys
- If your app hash environment variables set in .ZSHRC you will need to tell Heroku about them.

We need to provide our S3 key to heroku. 

(Let’s go on AWS and re-create a “wdi-london-sep-2013-first_name”, if you deleted it previously.)

Then in terminal:  
`$ heroku config:set AWS_ACCESS_KEY_ID=YOUR_INFO`  
`$ heroku config:set AWS_SECRET_ACCESS_ID=YOUR_INFO`  
`$ heroku config:set WDI_S3_BUCKET=wdi-london-sep-2013-first_name`  


If we now go back to our app and make changes, we will need to push these changes up to heroku to change our server version:
`$ gid add .`
`$ git commit -m "some commit"`
`$ git push heroku master`


#Resources

[Rails Heroku tutorial](http://railsapps.github.io/rails-heroku-tutorial.html)
[Heroku Cheatsheet](http://ruten.ca/2012/02/15/heroku-cheatsheet-useful-heroku-commands-reference/)


<https://devcenter.heroku.com/articles/rails-asset-pipeline>
