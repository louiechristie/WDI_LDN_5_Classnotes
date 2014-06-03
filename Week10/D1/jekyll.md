# JEKYLL FOR SIMPLE BLOGGING

http://jekyllrb.com/docs/home/


Sometimes we can get away with a simple static site, e.g.:

- a blog
- a promotional site

Static sites can be served very quickly and cheaply.

Jekyll lets us make static sites with dynamic tools:

  - layouts
  - partials
  - substitutions

To sum it up:
Jekyll + github-pages = good way to promote self & projects


## Static vs Dynamic

Rails is a tool for making dynamic sites:
  - routes programmatically associated with actions & views
  - views = html + substitutions (from db data + business logic)
  - server-side scripting needed
  - response times slow as pages built for each request

Once upon a time, we only had static sites:
  - routes followed folder structure of html pages
  - views = plain html
  - no scripting needed
  - response times very fast as page already rendered


Jekyll = static site + dynamic patterns


Dynamic frameworks give us some nice tools:
  - layouts
  - partials
  - substitutions (separation of data and views)
  - configurable routes

Jekyll lets us use these patterns to make a static site:
  - we design our site
  - jekyll builds all the pages
  - we host the pages on a cheap/free host


Thanks GitHub!


Conveniently, Jekyll was developed by GitHub. This means that Github will host our static site via GitHub pages - for free.

There are two types of GitHub pages:

user/organisational pages:

  live in master branch of special repo...
  e.g. Jon's is called jmchambers.github.io

project pages:

  live in gh-pages branch or a regular repo...
  e.g. the parslet gem
  the project pages are hosted at http://kschiess.github.io/parslet/


### Documentation


There is quite a bit of documentation available on jekyll:
http://jekyllrb.com/docs/usage
https://help.github.com/categories/20/articles



## getting started

There was a chat at LRUG in Feb 2013, talking about the ease of use and implementation of Jekyll... although there were a few glitches in the code in the slides (maybe because of version differences)
https://skillsmatter.com/skillscasts/5054-create-your-own-blog-using-jekyll

    gem install jekyll
    jekyll new myblog
    cd myblog

## Dir structure

    ├── config.yml 
    ├── _layouts 
    │     ├── default.html 
    │     └── post.html 
    ├── _posts 
    │     └── 2013-11-21-welcome-to-jekyll.markdown 
    ├── css 
    │     ├── main.css 
    │     └── syntax.css 
    └── index.html

Create a `README.md` file in the root and write some descriptive text explaining your blog site.


## Run it!

    jekyll serve --watch

Visit http://localhost:4000

Create new posts in the _posts directory.

When we started the server, jekyll automatically ran a jekyll `build` command that combined our data, layouts and templates to create a static site. 

Notice that our posts got placed in a nested folder structure that gives rise to the blog-like routes.

Non-html files also got copied into '_site'.

- - -

Notice that "README.md" got copied across as well (as would `GEMFILE` files if you'd created them).

These don't really have any role to play in a static site, so we can tell jekyll to exclude them by editing the '_config.yml'.

`_config.yml`

    exclude: ['README.md', 'GEMFILE', 'GEMFILE.lock']


## Liquid templating intro

"Liquid" is the templating system that Jekyll uses. 

The {{ }} syntax is equivalent to erb's <%= %> tags. 

In Liquid, the equivalent to erb's non-outputting tag, <% %>, is {% %}.

Liquid is a special templating library developed by shopify. 

What makes it special is that:
  - we can safely allow the end-user of our site to edit the templates
  - because we CANNOT run arbitrary code inside liquid tags
  - we can only substitute data as content, or use liquid's built in methods (e.g. for loop)

Jekyll uses liquid templating because it allows GitHub to safely run jekyll build for our site, on their servers, safe in the knowledge that we're not able to run arbitrary code. 

Because GitHub build our site for us, we don't need to include the "_site" folder in the repo we send to GitHub. 

That's why, if we take a look at the ".gitignore" file jekyll has generated, it deliberately omits the "_site" folder from the repo.


## Paginate

We can paginate our blog pages by setting a configuration value, and incorporating pagination functionality (and links).

`config.yml`

    paginate: 5

`index.html`

    {% for post in paginator.posts %}



    {% if paginator.next_page %}
      <a rel="next" href="{{ paginator.next_page_path }}">older</a>
    {% endif %}
    {% if paginator.previous_page %}
      <a rel="prev" href="{{ paginator.previous_page_path }}">newer</a>
    {% endif %}




## 'Partials'

Jekyll gives us access to templates to customise layouts, and also an equivalent to 'partials' that allow us to extract common, or repeated code.


`_layouts/default.html`

    <!DOCTYPE html>
    <html>
      {% include head.html %}
      <body>
        <div class="site">
          {% include header.html %}
          {{ content }}
          {% include footer.html %}
        </div>
      </body>
    </html>

Cut content from `default.html`

`_includes/head.html`

    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
        <title>{{ page.title }}</title>
        <meta name="viewport" content="width=device-width">

        <!-- syntax highlighting CSS -->
        <link rel="stylesheet" href="/css/syntax.css">

        <!-- Custom CSS -->
        <link rel="stylesheet" href="/css/main.css">
    </head>


`_includes/header.html`

    <div class="header">
      <h1 class="title"><a href="/">{{ site.name }}</a></h1>
      <a class="extra" href="/">home</a>
    </div> 


`_includes/footer.html`

    <div class="footer">
      <div class="contact">
        <p>
          Your Name<br />
          What You Are<br />
          you@example.com
        </p>
      </div>
      <div class="contact">
        <p>
          <a href="https://github.com/yourusername">github.com/yourusername</a><br />
          <a href="https://twitter.com/yourusername">twitter.com/yourusername</a><br />
        </p>
      </div>
    </div>
  </div>



## Substituting data

If we look at "_config.yml", we'll see that it contains the name key and value "Your New Jekyll Site". 

We can change this value to something else:
name: <Your name>'s Personal Page

We use "_config" for important, site-wide, data and settings. 

The markdown and pygments key/values are settings that tell jekyll:
  - what gem to use to convert markdown to html
  - whether or not we want to use the pygments gem for syntax highlighting code snippets


In `index.html` there is a block of text at the top which is referred to as "Front Matter".

    layout: default
    title: Your New Jekyll Site

This data is used in the layout file by accessing the `page` variable.

Front Matter is YAML syntax data.

Other data can be included from files in a `_data` folder.

`_data/social.yml`

    - name: Twitter 
      url: https://twitter.com/foo 
    - name: Google Plus 
      url: https://plus.google.com/bar

We can use this data in our views using the site.data prefix.

To that effect, let's edit our "footer.html", and replace the content of the "footer" div by:    

`_includes/footer.html`

    <div class="contact">
      <p>
        {% for link in site.data.social %}
          <a href="{{ link.url }}">{{ link.name }}</a><br />
        {% endfor %}
      </p>
    </div>


## Assets (css/js/images)

Any non-special files and folders are copied, as-is, to "_site". 

We can organise stylesheets, javascript and image files any way we see fit.



## Deployment

Let's get github to host our new personal site.

Make sure our site is committed:

    $ git init
    $ git add .
    $ git commit -m "initial commit"

Then on GitHub, create a new repo with the name:

<your github user name>.github.io

note: *exactly* your account name - it's case sensitive.

Then, use the instructions that GitHub supply for an existing repo:

    $ git remote add origin git@github.com:<your github user name>/<your github user name>.github.io.git 
    $ git push origin master

Now, we can visit <your github user name>.github.io in the browser. 

Behold! Our free personal blog is now online.


## Creating a project site

Github also lets you have a site for each of your repos. 

This is a common way of adding online docs for your project, e.g.: the parslet gem.

If we take a look at the parslet repo, we can see that it has a special branch called gh-pages, this is where GitHub looks for a project site.

We can create a site easily using the Github settings.

Step-by-step:

  - Create a new repo, "my_awesome_app"
  - Go to settings and follow the steps to add a page
  - Note that the page code is in the "gh-pages" branch
  - we can clone the repo and add things to "master"
    e.g. `$ rails new .`
  - then commit and push


### Gotcha with baseurl

Notice that our project page is served from github.io/repo_name/.

This affects the relative links to stylesheets and posts. 

We can visit http://jekyllrb.com/docs/github-pages/ for a quick way to fix this.



http://jekyllrb.com/docs/home/

