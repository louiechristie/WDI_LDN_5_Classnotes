# Web Scraping

I want to find links on google that have my name in them, and then to store them in a DB for me to follow later.

Let's start by writing a command-line script that does what we want.

We're going to use the "mechanize" gem to 'read' web pages in code.

`scrape.rb`

    require 'rubygems'
    require 'mechanize'
    require 'pry'

    def main
      agent = Mechanize.new

      page = agent.get('http://www.google.co.uk/')

      binding.pry
    end

    main


## Build up the 'scrape.rb' file

Extract the form from the Google page, populate the search term and submit the search.

    search_term = 'pavling'

    google_form = page.form('f')
    google_form.q = search_term
    page = agent.submit(google_form, google_form.buttons.first)

...and look at the resulting `page` variable...


Map all of the links on the page - we just want the text and url.

    links = page.links.map do |link|
      {
        text: link.text,
        uri: link.uri.to_s,
      }
    end


But there's a lot in there that's not relevant. I only want the links that are to Google's `url` pages, which have my search term in the title...

    links = page.links_with(text: Regexp.new(search_term, :i)).map do |link|
      # match anything beginning `http` which is followed by an ampersand (the `(?=x)` is a 'lookahead'), but only to the first ampersand (using the `?` as a non-greedy modifier on the `.*` quantifier)
      matched_uri = /http.*?(?=&)/.match(link.uri.to_s).to_s

      if !matched_uri.empty?
        {
          text: link.text,
          uri: matched_uri,
        }
      end
    end.compact!

Lastly, we want to follow the links to subsequent pages of results. These are the pages that are to Google's `search` page, which pass my search term as the query.

Since we'll want to do the same 'link mapping' for each page of links, we'll DRY:

    links = map_links(page, search_term)

    links.compact!

    page.links_with(href: Regexp.new("/search\\?q=#{search_term}&"), text: %r{\d+}).each do |result_page_link|
      puts "searching page"
      next_page = result_page_link.click
      links += map_links(next_page, search_term)
    end

    links.compact!

    def map_links(page, search_term)
      page.links_with(text: Regexp.new(search_term, :i)).map do |link|
        # match anything beginning `http` which is followed by an ampersand (the `(?=x)` is a 'lookahead'), but only to the first ampersand (using the `?` as a non-greedy modifier on the `.*` quantifier)
        matched_uri = /http.*?(?=&)/.match(link.uri.to_s).to_s

        if !matched_uri.empty?
          {
            text: link.text,
            uri: matched_uri,
          }
        end
      end
    end
    

## Tasks in Rails

So now we want to move this into a Rails app, and store the results in a "Link" model

    rails new pav_on_the_web
    cd pav_on_the_web
    rails g scaffold Link search_term text uri:text
    rake db:migrate
    gem 'mechanize'
    rails g task scrape for_search_term


We need to paste our `scrape.rb` code into the newly generated 


`lib/scrape.task`

    namespace :scrape do

      desc "Scrape Google for links referring to a given search term"
      task :for_search_term => :environment do
        search_term = ARGV.last.to_s

        raise "you've gotta give me a search term" if search_term.empty?

        # ... the rest of our scrape.rb file's `main` method, and the `map_links` method...

We expect to pass-in the search term when we call the task, so we use the `ARGV` collection to extract command-line arguments.

This highlights a quirk with rake tasks - if we pass multiple command-line params to rake, it assumes that the arguments we have passed are other rake tasks to call.

A workaround is to define a "fake" rake task for our search term (on the last line of our `scrape` task):

    task search_term.to_sym do ; end












