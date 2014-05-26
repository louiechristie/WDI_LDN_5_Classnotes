# jQuery UI

jQuery UI is a curated set of user interface interactions, effects, widgets, and themes built on top of the jQuery JavaScript Library. Whether you're building highly interactive web applications or you just need to add a date picker to a form control, jQuery UI is the perfect choice.

You can view the [api documentation](http://api.jqueryui.com/) here.

    git@github.com:Pavling/wdi-w7d3-jquery-ui.git

##Accordion

```
  <html>
    <head>
     <link rel="stylesheet" href="http://code.jquery.com/ui/1.10.3/themes/smoothness/jquery-ui.css" />
  # We will need to require jquery and the jquery-ui.
     <script src="http://ajax.googleapis.com/ajax/libs/jquery/2.0.0/jquery.min.js"></script>
     <script src="http://code.jquery.com/ui/1.10.3/jquery-ui.js"></script>

      <script>
          $(function() {
            $( "#accordion" ).accordion({
              heightStyle: "content", 
  // fill
  //event: "mouseover"
  //collapsible: true
            })

            $( "#accordion2" ).accordion({
              header: "> div > h3",
              heightStyle: "fill"
  // JQuery UI provides a sortable method for use on the content.
            }).sortable({
  // We will need to provide it with a y-axis.
              axis: "y",
              handle: "h3",
              stop: function( event, ui ) {
  // IE doesn't register the blur when sorting
  // so trigger focusout handlers to remove .ui-state-focus
                ui.item.children( "h3" ).triggerHandler( "focusout" );
              }
            });

          });
          
      </script>
    </head>
    <body>
  # The markup of your accordion container needs pairs of headers and content panels:
      <h1>Accordion</h1>
      <div class="ui-widget-content">
        <div id="accordion">
          <h3>Section 1</h3>
          <div>
            <p>Mauris mauris ante, blandit et, ultrices a, suscipit eget, quam. Integer ut neque. Vivamus nisi metus, molestie vel, gravida in, condimentum sit amet, nunc. Nam a nibh. Donec suscipit eros. Nam mi. Proin viverra leo ut odio. Curabitur malesuada. Vestibulum a velit eu ante scelerisque vulputate.</p>
          </div>
          <h3>Section 2</h3>
          <div>
            <p>Sed non urna. Donec et ante. Phasellus eu ligula. Vestibulum sit amet purus. Vivamus hendrerit, dolor at aliquet laoreet, mauris turpis porttitor velit, faucibus interdum tellus libero ac justo. Vivamus non quam. In suscipit faucibus urna. </p>
          </div>
          <h3>Section 3</h3>
          <div>
            <p>Nam enim risus, molestie et, porta ac, aliquam ac, risus. Quisque lobortis. Phasellus pellentesque purus in massa. Aenean in pede. Phasellus ac libero ac tellus pellentesque semper. Sed ac felis. Sed commodo, magna quis lacinia ornare, quam ante aliquam nisi, eu iaculis leo purus venenatis dui. </p>
            <ul>
              <li>List item one</li>
              <li>List item two</li>
              <li>List item three</li>
              <li>List item one</li>
              <li>List item two</li>
              <li>List item three</li>
              <li>List item one</li>
              <li>List item two</li>
              <li>List item three</li>
              <li>List item one</li>
              <li>List item two</li>
              <li>List item three</li>
            </ul>
          </div>
          <h3>Section 4</h3>
          <div>
            <p>Cras dictum. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Aenean lacinia mauris vel est. </p><p>Suspendisse eu nisl. Nullam ut libero. Integer dignissim consequat lectus. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. </p>
          </div>
        </div>
      </div>
    </body>
  </html>
```


## DatePicker

The jQuery UI Datepicker is a highly configurable plugin that adds datepicker functionality to your pages. You can customize the date format and language, restrict the selectable date ranges and add in buttons and other navigation options easily.

```
  <html>
    <head>
     <link rel="stylesheet" href="http://code.jquery.com/ui/1.10.3/themes/smoothness/jquery-ui.css" />
  # We will need to require jquery and the jquery-ui.
     <script src="http://ajax.googleapis.com/ajax/libs/jquery/2.0.0/jquery.min.js"></script>
     <script src="http://code.jquery.com/ui/1.10.3/jquery-ui.js"></script>
      <script>
          $(function() {
  // Initialize the datepicker with your desired options
            $( "#datepicker" ).datepicker({
              numberOfMonths: 4,
              showButtonPanel: true,
              showWeek: true,
              firstDay: 1,
  //            dayNamesMin: ["britney", "michael", "gerry", "kim", "salman", "jizzy", "gordon"],
              dateFormat: "yy-mm-dd"
            });
  
            $( "#from" ).datepicker({
              defaultDate: "+1w",
              changeMonth: true,
              numberOfMonths: 3,
              onClose: function( selectedDate ) {
                $( "#to" ).datepicker( "option", "minDate", selectedDate );
              }
            });
            $( "#to" ).datepicker({
              defaultDate: "+1m",
              changeMonth: true,
              numberOfMonths: 3,
              onClose: function( selectedDate ) {
                $( "#from" ).datepicker( "option", "maxDate", selectedDate );
              }
            });
  
          });
          
      </script>
    </head>
    <body>
      <h1>Datepicker</h1>
      <div>
        <p>Date: <input type="text" id="datepicker" /></p>
        <hr>
        <p>
          <label for="from">From</label>
          <input type="text" id="from" name="from" />
  
          <label for="to">to</label>
          <input type="text" id="to" name="to" />
        </p>
      </div>
  
    </body>
  </html>
```


## Sortable

The jQuery UI Sortable plugin makes selected elements sortable by dragging with the mouse.

```
<html>
  <head>
   <link rel="stylesheet" href="http://code.jquery.com/ui/1.10.3/themes/smoothness/jquery-ui.css" />
# We will need to require jquery and the jquery-ui.
   <script src="http://ajax.googleapis.com/ajax/libs/jquery/2.0.0/jquery.min.js"></script>
   <script src="http://code.jquery.com/ui/1.10.3/jquery-ui.js"></script>    
   <script>
      $(function() {
        $( "#sortable" ).sortable();
        $( "#sortable" ).disableSelection();
      });
    </script>
  </head>
  <body>
    <ul id="sortable">
      <li class="ui-state-default"><span></span>Item 4</li>
      <li class="ui-state-default"><span></span>Item 8</li>
      <li class="ui-state-default"><span></span>Item 3</li>
      <li class="ui-state-default"><span></span>Item 1</li>
      <li class="ui-state-default"><span></span>Item 7</li>
      <li class="ui-state-default"><span></span>Item 2</li>
      <li class="ui-state-default"><span></span>Item 6</li>
      <li class="ui-state-default"><span></span>Item 5</li>
    </ul>

  </body>
</html>
```


## Draggable

Draggable will enable draggable functionality on any DOM element. 

```
<html>
  <head>  
   <link rel="stylesheet" href="http://code.jquery.com/ui/1.10.3/themes/smoothness/jquery-ui.css" />
# We will need to require jquery and the jquery-ui.
   <script src="http://ajax.googleapis.com/ajax/libs/jquery/2.0.0/jquery.min.js"></script>
   <script src="http://code.jquery.com/ui/1.10.3/jquery-ui.js"></script>    
    <script>
        $(  function() {
// We will need to call the draggable method on the element we want to make draggable.
          $( "#catalog li" ).draggable({
            appendTo: "body",
            helper: "clone"
          });
// The jQuery UI Droppable plugin makes selected elements droppable (meaning they accept being dropped on by draggables). You can specify which draggables each will accept.
          $( "#cart ol" ).droppable({
            activeClass: "ui-state-default",
            hoverClass: "ui-state-hover",
            accept: ":not(.ui-sortable-helper)",
            drop: function( event, ui ) {
              $( this ).find( ".placeholder" ).remove();
              $( "<li></li>" ).text( ui.draggable.text() ).appendTo( this );
            }
          }).sortable({
            items: "li:not(.placeholder)",
            sort: function() {
// gets added unintentionally by droppable interacting with sortable
// using connectWithSortable fixes this, but doesn't allow you to customize active/hoverClass options
              $( this ).removeClass( "ui-state-default" );
            }
          });
        });
    </script>
  </head>
  <body>
    <div id="products">
      <h1 class="ui-widget-header">Ruby</h1>
      <div id="catalog">
        <h2><a href="#">Pre-processors</a></h2>
        <div>
          <ul>
            <li>Haml</li>
            <li>Sass</li>
            <li>Slim</li>
            <li>Coffeescript</li>
          </ul>
        </div>
        <h2><a href="#">DBs</a></h2>
        <div>
          <ul>
            <li>PgSql</li>
            <li>MySql</li>
            <li>Mongodb</li>
            <li>Cassandra</li>
          </ul>
        </div>
      </div>
    </div>
     
    <div id="cart">
      <h1 class="ui-widget-header">What I use</h1>
      <div class="ui-widget-content">
        <ol>
          <li class="placeholder">Add your items here</li>
        </ol>
      </div>
    </div>

  </body>
</html>
```

## Slider

The jQuery UI Slider plugin makes selected elements into sliders. There are various options such as multiple handles and ranges. The handle can be moved with the mouse or the arrow keys.

```
  <html>
    <head>
     <link rel="stylesheet" href="http://code.jquery.com/ui/1.10.3/themes/smoothness/jquery-ui.css" />
  # We will need to require jquery and the jquery-ui.
     <script src="http://ajax.googleapis.com/ajax/libs/jquery/2.0.0/jquery.min.js"></script>
     <script src="http://code.jquery.com/ui/1.10.3/jquery-ui.js"></script>
      <script>
          $(function() {
            $( "#slider-range" ).slider({
  // A min range goes from the slider min to one handle. A max range goes from one handle to the slider max.
              range: true,
              min: 0,
              max: 500,
              values: [ 75, 300 ],
              slide: function( event, ui ) {
  // Triggered on every mouse move during slide. The value provided in the event as ui.value represents the value that the handle will have as a result of the current movement. Canceling the event will prevent the handle from moving and the handle will continue to have its previous value.
                $( "#amount" ).val( "$" + ui.values[ 0 ] + " - $" + ui.values[ 1 ] );
              }
            });
            $( "#amount" ).val( "$" + $( "#slider-range" ).slider( "values", 0 ) +
              " - $" + $( "#slider-range" ).slider( "values", 1 ) );
          });
      </script>
    </head>
    <body>
      <h1>Slider</h1>
      <div>
        <p>
          <input id="amount"/>
        </p>
         
        <div id="slider-range"></div>
      </div>
    </body>
  </html>
```

## Tooltips

Tooltip replaces native tooltips, making them themeable as well as allowing various customizations:
Display other content than just the title, like inline footnotes or extra content retrieved via Ajax.
Customize the positioning, e.g., to center the tooltip above elements.

Add extra styling to customize the appearance, for warning or error fields.

A fade animation is used by default to show and hide the tooltip, making the appearance a bit more organic, compared to just toggling the visibility.


```
  <html>
    <head>
      <link rel="stylesheet" href="http://code.jquery.com/ui/1.10.3/themes/smoothness/jquery-ui.css" />
      <script src="http://ajax.googleapis.com/ajax/libs/jquery/2.0.0/jquery.min.js"></script>
      <script src="http://code.jquery.com/ui/1.10.3/jquery-ui.js"></script>
    
      <script>
          $(function() {
            $( document ).tooltip({
  // position identifies the position of the tooltip in relation to the associated target element.
              position: {
                my: "center bottom-20",
                at: "center top",
                using: function( position, feedback ) {
                  $( this ).css( position );
                  $( "<div>" )
                    .addClass( "arrow" )
                    .addClass( feedback.vertical )
                    .addClass( feedback.horizontal )
                    .appendTo( this );
                }
              }
            });
          });
      </script>
    </head>
    <body>
      <h1>Tooltip</h1>
      <div>
        <p><a href="#" title="That's what this widget is">Tooltips</a> can be attached to any element. When you hover the element with your mouse, the title attribute is displayed in a little box next to the element, just like a native tooltip.</p>
        <p>But as it's not a native tooltip, it can be styled. Any themes built with
        <a href="http://themeroller.com" title="ThemeRoller: jQuery UI's theme builder application">ThemeRoller</a> will also style tooltips accordingly.</p>
        <p>Tooltips are also useful for form elements, to show some additional information in the context of each field.</p>
        <p><label for="age">Your age:</label> <input id="age" title="We ask for your age only for statistical purposes." /></p>
        <p>Hover the field to see the tooltip.</p>
      </div>
    </body>
  </html>
```


[https://github.com/joliss/jquery-ui-rails](https://github.com/joliss/jquery-ui-rails)
