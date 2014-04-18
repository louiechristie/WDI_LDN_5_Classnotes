#RAILS VIEWS & HELPERS


As with Sinatra, we use "erb" to render a dynamic html. Luckily for us, Rails offers a lot of `helpers` to make our task easier.

Let’s have a look at the form created by the scaffold:

```
<%= form_for(@post) do |f| %>
  <% if @post.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@post.errors.count, "error") %> prohibited this post from being saved:</h2>
      <ul>
      <% @post.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
      </ul>
    </div>
  <% end %>
```

```
  <div class="field">
    <%= f.label :title %><br />
    <%= f.text_field :title %>
  </div>
  <div class="field">
    <%= f.label :content %><br />
    <%= f.text_area :content %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

We can spot a few interesting helpers in the example above:  

* form_for
* form_tag
* pluralize
* label
* text_field
* etc…

Rails extends Ruby. It offers us a lot more "helper" functionality on top of the Ruby basics. 

For instance:  

* ".blank?"
* ".to_sentence"

View helpers are generally designed to make our life easier; to simplify common tasks in views.

Some other common view helpers are:

###View helpers:

* UrlHelper:

` link_to`  
`link_to 'Destroy', recipe, method: :delete, data: {confirm: "Are you sure"}`

* FormTagHelper and FormHelper:

```
<%= form_tag('/posts') do -%>
  <div><%= submit_tag 'Save' %></div>
<% end -%>
```

`text_field_tag 'ip', '0.0.0.0', :maxlength => 15, :size => 20, :class => "ip-input"
`  

* NumberHelper:

`number_to_currency`  
`number_to_currency(1234567890.50, :unit => "&pound;", :separator => ",")`  

```
number_to_human  
number_to_human(1234)                             # => "1.23 Thousand"
number_to_human(12345)                            # => "12.3 Thousand"
number_to_human(1234567)                          # => "1.23 Million"
number_to_human(1234567890)                       # => "1.23 Billion"
number_to_human(1234567890123)                    # => "1.23 Trillion"
number_to_human(1234567890123456)                 # => "1.23 Quadrillion"
number_to_human(1234567890123456789)              # => "1230 Quadrillion"
number_to_human(489939, :precision => 2)
```

```
number_to_human_size
number_to_human_size(1234567890)                  # => 1.15 GB
```

* DateHelper:

`distance_of_time_in_words(from_time, to_time = 0, include_seconds = false, options = {})`  
`time_ago_in_words(from_time, include_seconds = false, options = {}) public`  
  
* TextHelper:

```
pluralize
pluralize(1, 'person')               # => 1 person
pluralize(2, 'person')               # => 2 people
pluralize(3, 'person', 'users')      # => 3 users
```

```    
truncate("Once upon a time in a world far far away") # => "Once upon a time in a world..."
```
    
* TagHelper:

```
tag
tag("br")
tag("div", :data => {:name => 'Stephen', :city_state => %w(Chicago IL)}) 
tag("input", :type => 'text', :disabled => true) # note the options hash is not wrapped in hash-tags
tag("input", {:type => 'text', :disabled => true}) # you'll see this a lot in Rails
```

```
content_tag(:div, "Hello world!")
<%= content_tag :div, :class => "strong" do -%>
  Hello world!
<% end -%>
```

###Others form helpers:

* datetime_tag
* file field tag

##Time and Date helpers (more examples): 

* time_ago_in_words(3.minutes.from_now) # => 3 minutes
* time_ago_in_words(3.minutes.ago) # => 3 minutes
* time_ago_in_words(Time.now - 15.hours) # => about 15 hours
* time_ago_in_words(Time.now) # => less than a minute
* time_ago_in_words(Time.now, include_seconds: true) # => less than 5 seconds

* from_time = Time.now - 3.days - 14.minutes - 25.seconds
* time_ago_in_words(from_time) # => 3 days

* from_time = (3.days + 14.minutes + 25.seconds).ago
* time_ago_in_words(from_time) # => 3 days

###Link helpers:

* link_to(body, url, html_options = {})
* link_to(body, url_options = {}, html_options = {})

#ADDITIONAL RESOURCES

Rails views & helpers:  
<http://apidock.com/rails/ActionView/Helpers/>  
<http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html>
