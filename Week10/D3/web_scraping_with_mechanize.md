# Web Scraping

I want to find links on google that have my name in them, and then to store them in a DB for me to follow later.

Let's start by writing a command-line script that does what we want.

We're going to use the "mechanize" gem to 'read' web pages in code.

    // mechawhat.rb
    require 'rubygems'
    require 'mechanize'
    require 'pry'

    def main
      agent = Mechanize.new

      page = agent.get('http://www.google.co.uk/')

      binding.pry
    end

    main

## Build up the 'mechawhat.rb' file

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

    links = page.links.map do |link|
      if link.text =~ Regexp.new(search_term, :i) && !((matched_uri = /http.*?(?=&)/.match(link.uri.to_s).to_s).empty?)
        {
          text: link.text,
          uri: matched_uri,
        }
      end
    end.compact!

Lastly, we want to follow the links to subsequent pages of results. These are the pages that are to Google\s `search` page, which pass my search term as the query.

Since we'll want to do the same 'link mapping' for each page of links, we'll DRY:

    links = map_links(page.links, search_term)

    links.compact!

    page.links_with(href: Regexp.new("/search\\?q=#{search_term}&"), text: %r{\d+}).each do |result_page_link|
      puts "searching page"
      next_page = result_page_link.click
      links += map_links(next_page.links, search_term)
    end



    def map_links(links, search_term)
      links.map do |link|
        if link.text =~ Regexp.new(search_term, :i) && !((matched_uri = /http.*?(?=&)/.match(link.uri.to_s).to_s).empty?)
          {
            text: link.text,
            uri: matched_uri,
          }
        end
      end
    end
    

So now we want to move this into a Rails app, and store the results in a "Link" model

  rails new pav_on_the_web
  cd pav_on_the_web
  rails g scaffold Link search_term text uri:text
  rake db:migrate
  rails g task scrape for_search_term

  gem 'mechanize'