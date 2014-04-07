## HTML

HTML stands for "Hyper Text Markup Language". It is not a programming language, as it doesn't send any instruction to your computer. However, it does send instructions to the browser (Chrome, Firefox, Safari, etc.), telling it what to display on the screen. 

Example:
In your "classwork" folder:   

`$ mkdir html`  
`$ cd html `  
`$ touch demo_1.html`  
`$ subl demo_1.html`  

```
<!DOCTYPE html>
<html>
  <body>
    <h1>My first webpage!</h1>
    <p>Hello, world!</p>

    <p>
      This word is <b>bold</b>.
    </p>

    <p>This word is <i>italic</i>.</p>

    <h2>Lists</h2>

    <h3>Ordered list</h3>
      <ol>
        <li>Call mom</li>
        <li>Buy milk</li>
      </ol>

    <h3>Unordered list</h3>
      <ul>
        <li>Apple</li>
        <li>Orange</li>
      </ul>
    
    <img src="http://www.misiide.net/images/2013/03/Tim-Berners-Lee.jpg" alt="A picture of Tim Berners-Lee!" />   

    <a href="www.google.com" target="_blank">Google</a>

  </body>
<html>
```

When we open our file in Chrome... "Hello, world!" Congratulations, you've just built your first webpage!

###Example of tags in HTML:

`<!DOCTYPE html>  ` → informs the browser that this file is an HTML file

`<html>...</html> ` → contains your html content - it will tell the browser that everything within these tags should be interpreted as HTML.

`<body>...</body>`    → contains the body of the page we will display.

`<p>...</p> `   → paragraph

`<b>...</b> `   → makes the wrapped word/text bold.

`<strong>...</strong>`  → a bit similar to bold, the browser interprets this as an important bit of text, and will direct the reader's attention to it.

`<i>...</i>`      → makes the wrapped word/text italic.

`<em>...</em>`    → a bit similar to italic, used by the browser to add emphasis on a word.

`<h1>...</h1>` to` <h6>...</h6>` → headings, from big to small

`<ul>...</ul>`    → unordered list (bullet points)

`<ol>...</ol>`    → ordered list (numbered)

`<li>...</li>`    → elements to be put within the ordered/unordered list tags

`<br>`        → self-closing tag, allowing you to break the content (CSS will allow us to achieve the same result, in a better way).

`<img src="url" alt="description" /> `→ image tag. This is a self-closing tag, meaning you don't need a </img> tag. It should include the source of the file (can be a url or a file path) and a description (alt) for the search engines. 

`<a href="url">...</a>` → link to another page; needs to include a href, which is the url it is linking to. You can add a `target="_blank` which will open the link into a new tab in the browser. We can also wrap images within `<a>` tags to transform it into a link.

In our code example above:

```  
<a href="www.w3.org" target="_blank">
  <img src="http://www.misiide.net/images/2013/03/Tim-Berners-Lee.jpg" alt="A picture of Tim Berners-Lee!" />
</a>
``` 
 
→ this makes the image "clickable", and redirects to the W3 website.

`<span>...</span>`  → inline element, allows to isolate a bit of text and apply it a style with CSS (we'll see this later on today). SPAN elements take only the space they need on the page, as opposed to DIV elements.

`<div>...</div>`    → block element, these elements take up the whole width of the page, unless specific style is applied to them with CSS. They can contain paragraphs, headings, text, images, other divs, etc. They work as a way to structure the page with clearly delimited blocks. 

**Creating a table in HTML**

We use the `<table>` tags, and can pass them some attributes. We will learn to do this in CSS soon.

Example:

```
  <table border="1">
    <thead>
        <tr>
          <th>Date</th>
          <th>Weight</th>
          <th>Distance walked</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>September 15</td>
          <td>75 kg</td>
          <td>1.8 km</td>
        <tr>
        <tr>
          <td>September 29</td>
          <td>73 kg</td>
          <td>2.1 km</td>
        <tr>
      </tbody>
  </table>
```

In our browser, we get:

Date | Weight | Distance Walked
-----|--------|-----------------
September 15 | 75 kg |1.8 km
September 29 |73 kg | 2.1 km

Table-specific tags:

`<table>...</table>`  → contains the table data, and defines the table structure  
`<thead>...</thead>`  → the head of the table (bolder text) - optional  
`<tr>...</tr>`    → defines a row  
`<th>...</th>`    → defines a cell within that row  
`<tbody>...</tbody>`  → the body of the table   

REMEMBER: Don't use tables to define the layout of a page! 

**Introduction to <head> tags**

When opening a new html file, it is important to include a "head".

```
  <head>
      <title>Page's title</title>
      <meta name="description" content="...">
      <meta name="keywords">
  </head>
```

The "head" is important for search engines, as it helps provide additional information about the website. Anything within the <head> tags will NOT be displayed on the page. 

We had these tags right after the opening `<html>` tag, and before the opening `<body>` tag.

It is important to mention that ` <meta>` "content" and "keywords" are outdated HTML elements, and most recent webpages don't use them anymore.


####Google Chrome Developer Tools

Let us have a quick first look at **Chrome Developer Tools**. Available as a console in Google Chrome, it allows us to get a lot of information about the page we're on, providing a detailed look into the HTML structure of the page and the CSS styling, among other things. 

In Chrome, you can access it with `Cmd+Alt+i`, or right-click "Inspect element".

As of now, let's mainly look at the "Elements" tab → it's a very powerful way to look at the page structure, and locate specific elements within the page.

You can also gain some useful information on all of the elements by looking at the data on the right column of the console, most notably the CSS properties currently applied to the elements... and change them "live" (these changes, however, only apply to the page as displayed - it will not be saved in your CSS file, and all these changes disappear on the next page reload). 

We will get to play with this functionality more as we dig deeper into the CSS chapter. 


##ADDITIONAL RESOURCES

HTML:

* Online HTML live editor: <https://thimble.webmaker.org/en-US/projects/wrangler/>
* HTML element reference: <https://developer.mozilla.org/en-US/docs/Web/HTML/Element>
* Tim Berners-Lee: <http://www.w3.org/People/Berners-Lee/>
* Evolution of the Web: <http://www.evolutionoftheweb.com/> (you'll love it)
* Semantic HTML: <http://en.wikipedia.org/wiki/Semantic_HTML>




