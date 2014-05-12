# SASS/SCSS

## Sass

Sass is a CSS pre-processor with syntax advancements. Stylesheets in the advanced syntax are processed by the program, and turned into regular CSS style sheets.

Sass helps us write CSS in a cleaner, faster and more organized way. It also has very nice features that help us reuse code and thus write DRYer CSS documents.
⇒ When using Sass, you'll write more efficient and time-saving CSS.

There are 2 Sass syntaxes:
Sass (indented syntax): older version, similar to Haml.
SCSS (Sassy CSS): newer syntax, inspired in CSS3. Valid CSS3 is also valid SCSS.


We work with the stylesheets today. There is one default folder in this directory, the "application.css" file. This file automatically "requires" all our other css files.

When we generate a controller, rails generates a new file in this directory, with a ".css.scss" extension.

Let's have a look at our "application.css" file, found at "assets/stylesheets/":
/*
 * This is a manifest file that'll be compiled into application.css, which will include all the files listed below.
 *
 * Any CSS and SCSS file within this directory, lib/assets/stylesheets, vendor/assets/stylesheets,
 * or vendor/assets/stylesheets of plugins, if any, can be referenced here using a relative path.
 *
 * You're free to add application-wide styles to this file and they'll appear at the top of the compiled file, but it's generally better to create a new file per style scope.
 *
 *= require_self
 *= require_tree .
 */

⇒ This file is present in every rails app we create.

We will see that the order in which we require files is very important in in css. It will render from top to bottom.


## SCSS

Sass is an extension of CSS3, adding nested rules, variables, mixins,selector inheritance, and more. It's translated to well-formatted, standard CSS using the command line tool or a web-framework plugin.

As we saw earlier, Sass has two syntaxes. The most commonly used syntax is known as "SCSS" (for "Sassy CSS"), and is a superset of CSS3's syntax. This means that every valid CSS3 stylesheet is valid SCSS as well. SCSS files use the extension .scss.

The second, older syntax is known as the indented syntax (or just ".sass"). Inspired by Haml's terseness, it's intended for people who prefer conciseness over similarity to CSS. 
Instead of brackets and semicolons, SASS uses the indentation of lines to specify blocks. Files in the indented syntax use the extension .sass.


## Nesting

Often when writing CSS, we'll have several selectors that all begin with the same thing. 
For example, you might have "#navbar ul", "#navbar li", and "#navbar li a". 

Having to repeat ourselves can bequite tedious, especially when our file gets long.

Sass allows us to avoid this by nesting the child selectors within the parent selector.


### Variables

Sass allows us to declare variables that can be used throughout our stylesheet.

Variables begin with $ and are declared just like properties. They can have any value that's allowed for a CSS property, such as colors, numbers (with units), or text.


### Mixins

Mixins are one of the most powerful Sass features. They allow re-use of styles – properties or even selectors – without having to copy and paste them, or move them into a non-semantic class. 

The real power of mixins comes when we pass them arguments. Arguments are declared as a parenthesized, comma-separated list of variables. Each of those variables is assigned a value each time the mixin is used.


## Practice

Since we've got a newly created app, HamlApp, at our disposal, let's keep practicing with it. We can discover how to write SASS/SCSS by transforming our existing file. 

First, in "app/assets/stylesheets", we start by renaming our "styles.css" into "styles.scss".

Let's add a border to our divs, in the "styles.scss" file:

    body {
      background-color: lightblue;
    }
    div {
      border: 1px solid black;
      color: white;
      margin: 10px;
      padding: 10px;
      width: 256px;
    }
    #content { 
      color: blue;
    }
    #footer {  
      color: blue;
    }
    #div_1 {
      background-color: black;
      width: 300px;
    }
    #div_2 {
      background-color: yellow;
    }
    #div_3 {
      background-color: blue;
    }
    #div_4 {
      background-color: red;
    }



As explained above, we can declare variables.

Still in our "styles.scss" file:

    $text-color: orange; // now we only need to change the variable's value here...

    div {
      border: 1px solid black;
      color: $text-color; // and it will be changed everywhere at once.
      margin: 10px;
      padding: 10px;
      width: 256px;
    }

    #content { 
      color: $text-color; // Like here... (changed color for variable)
    }

    # footer { 
      color: $text-color; // ...and here (changed color for variable)
    }

- - - 

We can nest our elements within others in our stylesheet. 

For instance, we want to target the "p" element inside our footer and header divs. 

We could usually do it this way:

    #header p {
      color: green;
    }

    #footer p {
      color: yellow;
    }

... but we can achieve this more efficiently. 

Since, we've already defined a footer class earlier, let's nest the "p" element inside of it:

    # footer { 
      color: $text-color;
      p {
        color: yellow;
      }
    }

Even more targeted nested is also possible:
Let's say we have defined a new class, "very_big", and that this class is applied only to one of the "p" elements nested inside our footer.

    .very-big {
      font-size: 4em;
    }

    # footer { 
      color: $text-color;
      p {
        color: yellow;
        
        &.very-big { /*  notice the "&" */
          color: blue;
        }

      }
    }

... the "&" allows us to target the "p" element inside that footer that has a "very-big" class, and pass it additional arguments. 

Without the "&", we would basically be applying to the "very-big" class on any elements nested inside the footer's "p" tags.

Another example of this "&":

Let's say we have a list in our Haml file.

```
%ul.social_buttons
  %li.twitter
  %li.fb
  %li.gplus
```

In our "styles.scss" file, we'd have

    ul.social_buttons {
      li.twitter {
        background-image: twitter;
      }
      li.fb {
        background-image: fb;
      }
      li.gplus {
        background-image: gplus;
      }

If I want them all to have a padding of 20px:

```
    li {
      padding: 20px;
      &.li.twitter { background-image: twitter; }
      &.li.fb { background-image: fb; }
      &.li.gplus { background-image: gplus; }
    }
```

=> much DRYer!

- - -

Finally, let's have a closer look at the mixins we mentioned earlier.

We can simply define a mixin in our stylesheet:

    @mixin border-radius($radius) {
      -webkit-border-radius: $radius;
      -moz-border-radius: $radius;
      border-radius: $radius;
    }

=> it can take a parameter (here, $radius, a variable).

And then use it at different places throughout the stylesheet:

    #footer {
      @include border-radius(25px); /* and we're passing it the radius we want.*/
    }

Another mixin example:

    @mixin centered-text($color) {
      text-align: center;
      color: $color;
    }

...which can they reuse:

    #footer {
      @include border-radius(25px);
      @include centered-text(yellow); /* and we're passing it the color we want. */
    }


## Another round of practice

Let's pretend we've been working on a Blog app
We will create some variables, and use them in our stylesheet.

We have a "blog_post.css.scss":

    $sans-serif: Halvetica, Arial, Tahoma, sans-serif;
    $serif: 'Times New Roman', Time, serif;
    $christmas_red: #456456;
    $white: #fff;
    body{
      background: $christmas_red;
      font 100%/1.5 $serif;
      color: $white;
    }

- - -

Now, we can nest some elements.

    ul {
      list-style: none;
      padding-left: 0;
      li {
        border-bottom: 4px solid white;
      }
    }

This is DRYer than css as it saves us from writing:

    ul {
      list-style: none;
      padding-left: 0;
    }

    ul li {
      border-bottom: 4px solid white;
    }

We can also nest our pseudoclasses:

    ul {
      list-style: none;
      padding-left: 0;
      li {
        @include rounded-box(20px);
        border-bottom: 4px solid white;
        &:hover {
          color: green;
        }
      }
    }

- - -

Now we will create a mixin:

    @mixin rounded-box {
      border-radius: 10px;
      background: blue;
    }

    ul {
      list-style: none;
      padding-left: 0;
      li {
        @include rounded-box;
        border-bottom: 4px solid white;
      }
    }

Now we can pass our mixin an argument:

    @mixin rounded-box($radius) {
      border-radius: $radius;
      background: blue;
    }
    ul {
      list-style: none;
      padding-left: 0;
      li {
        @include rounded-box(20px);
        border-bottom: 4px solid white;
      }
    }

- - - 

Interestingly, we can also perform operations in our scss:

    ul {
      list-style: none;
      padding-left: 0;
      li {
        width: 200px / 2;
        @include rounded-box(20px);
        border-bottom: 4px solid white;
      }
    }

And we can manipulate colours:

    @mixin rounded-box($radius) {
      border-radius: $radius;
      background: lighten(blue, 10%);
    }


Even more practice: let's convert this code!

    ul.menubar {
      background: white;
      list-style: none;
      display: block;
      padding: 0 10px;
    }

    ul.menubar li {
      display: inline-block;
      position: relative;
    }

    ul.menubar li a {
      color: black;
      display: block;
      padding: 10px 14px;
      text-decoration: none;
    }

    ul.menubar li a:hover {
      background: #29a7f5;
      color: white;
    }

    ul.menubar li ul {
      list-style: none;
      display: block;
      position: absolute;
      top: 100%;
      background: white;
      padding: 10px 0;
    }

    ul.menubar li ul li a {
      color: #29a7f5;
      display: block;
      padding: 8px 20px;
      text-decoration: none;
    }

    ul.menubar li ul li a:hover {
      background: #29a7f5;
      color: white;
    }

    ul.menubar li.is-selected a {
      background: #29a7f5;
      color: white;
    }

    ul.menubar li.is-selected ul {
      list-style: none;
      display: block;
    }

...can be converted into:

    ul.menubar {
      background: white;
      list-style: none;
      display: block;
      padding: 0 10px;

      li {
        display: inline-block;
        position: relative;

        a {
          color: black;
          display: block;
          padding: 10px 14px;
          text-decoration: none;

          &:hover { background: #29a7f5; color: white; }
        }

        ul {
          list-style: none;
          display: block;
          position: absolute;
          top: 100%;
          background: white;
          padding: 10px 0;

          li a {
            color: #29a7f5;
            display: block;
            padding: 8px 20px;
            text-decoration: none;

            &:hover { background: #29a7f5; color: white; }
          }
        }

        &.is-selected {
          a { background: #29a7f5; color: white; }
          ul {
            list-style: none;
            display: block;
          }
        }
      }
    }
