# View Partials

  - View Partials are another way of streamlining your code by breaking it into manageable chunks

  - Simply call "render 'filename'" from your view (or specify the path in the filename)

  - Often used to render the content of looped blocks or shared content (like forms - create/edit use the same form)

    `<%= render "menu" %>`  
    `<%= render partial: "menu" %>`  
      will render the file '**_menu.html.erb**' from the current view's path with all the current instance variables

    `<%= render "shared/menu" %>`   
      will share the '**app/views/shared/_menu.html.erb**'

  - Partials have the same access to instance variables as views do, but need to be passed any local variables (like we have to with methods)
    - some people argue that partials *should not* use instance vars, since they can't guarentee where they're going to be included from, they won't 'know' what values have been set

    
```
    <table>
      <% @blogs.each do |blog| %>
        <%= render partial: "blog", locals: {blog: blog} %>
      <% end %>
    </table>
```

```
    // _blog.html.erb
    <tr>
      <td>
        <%=blog.subject%>
      </td>
    </tr>

```

   - Every partial also has a local variable with the same name as the partial (minus the underscore). You can pass an object in to this local variable via the :object option:

     ` <%= render partial: "customer", object: @new_customer %>`
      
      Within the customer partial, the 'customer' variable will refer to `@new_customer` from the parent view.

   - To render a @customer instance of a Customer object with a _customer.html.erb partial:

      `<%= render @customer %>`

   - Simpler collection rendering
      - to simplify our @blogs example:

```
    <table>
      <%= render partial: "blog", collection: @blogs %>
    </table>
```

  or even:  
      `<%= render @blogs %>`
  

#ADDITIONAL RESOURCES

Layouts and rendering:  
<http://guides.rubyonrails.org/v3.2.16/layouts_and_rendering.html>