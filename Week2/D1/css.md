## CSS

If HTML is a set of instructions telling the browser WHAT to display, CSS (which stands for "Cascading Style Sheet") tells it HOW to display it. It provides the browser with precise instructions on how to style each element we want displayed on the page. 

It can affect the text format (font, size, color, etc.), the size and position of various objects on the page, etc. 

### Syntax

We build our CSS with a selector (usually the name of the html tag, but it can also be a specific class of elements, or an element with a unique ID).

```
selector {
  property_1: value_1;
  property_2: value_2;
}
```

Do not forget the curly brackets, or the semi-colon after the value! 


###Linking the stylesheet to the html file

Within the `<head>` tags, we need to add a self-closing `<link>` tag, indicating the type of relations (`rel="stylesheet"`) and the file path (`href="style.css"`):
`<link rel="stylesheet" href="style.css">`

We often have a specific folder for stylesheets, as we can have several on one application. For now, we placed the style.css file within the same folder as our html file, making it easy to target it (href="style.css")


### Using CSS

Using our previous example, let's build a style sheet. 

`$ touch style.css`  
`$ subl style.css`

In "style.css":

```
p {
  color: orange;
}
```
→ will change the color of paragraphs to "orange"

```
div {
  border: 1px solid black;
}
```

→ will add a 1px black border to each DIV on the page, since the selector targets the "div" elements.

To comment out something in CSS, we use the /* ... */ syntax.

Example:

```
div {
  /* border: 1px solid black; */
  border-width: 1px;
  border-style: solid;
  border-color: black;
}
```

You can also change the background color of elements ("background-color"), as well as the color of the text contained in these elements ("color"). Be careful not to confuse the two!

```
div {
  background-color: blue;
  color: white
  witdth: 100px;
  height: 50px;
}
```

###"id" and "class" attributes in CSS

We can apply "class" and "id" attributes to elements in html. It allows us to target these specific elements in CSS.

Example:

Let's first look at the id attribute:

In our HTML file: 


```
  <div id="div_1">
    ...some content...
  </div>

  <div id="div_2">
    ...some content...
  </div>

  <div id="div_3">
    ...some content...
  </div> 

```

Now within our "style.css" file:

```
div #div_1 {
background-color: green;
} 
```

→ this allows us to target specific elements on the page (here, only the div with the ID "div_1"). We can give any name we want to our ID attribute (besides the obvious reserved words, such as tag names, etc.).

The class attribute allows us to target not one, but several elements that may share similarities. 

```
  <div id="div_1">
      <div class="red_bg">
        ...some content...
      </div>
      <div class="red_bg">
        ...some content...
      </div>
  </div>
```

Now within our "style.css" file:

```
div.red_bg {
background-color: red;
} 
```

⇒ In CSS, we will target:

* an element with a specific "id" with an `#id` selector
* an element with a specific "class" with a `.class` selector

REMEMBER: today, the web is moving away from "id", and focusing more on the usage of the "class" attribute.


**Other examples**


Let's build other classes:

```
.text-right {
  text-align: right;
}
```

→ by applying this class to elements in our HTML file, we can position the text they contain on the right. 

```
.comic-sans {
  font-family: "Comic Sans MS";
}
```

→ by applying this class to elements in our HTML file, we can apply this particular font to targeted elements. 

```
.hidden {
  visibility: hidden;
}
```

→ will hide the targeted element, but the space where it should be on the page still exists.

```
.gone {
  display: none;
}
```
→ will not only hide the targeted element, but also remove it completely from the flow of the page, getting rid of the place that was allocated for it.


You can also chain these classes together, applying several classes to one element:

```
  <div class="gone comic-sans hidden">
      ...some content...
  </div>
```

As we can imagine, the possibilities are endless. There are many properties and values to work with, and many ways to target specific elements. Two pages could have the same HTML content, and yet look dramatically different, due to different CSS stylesheets.


###CSS Box Model

All HTML elements can be considered as boxes. In CSS, the term "box model" is used when talking about design and layout.

The CSS box model is essentially a box that wraps around HTML elements, and it consists of: margins, borders, padding, and the actual content.

The box model allows us to place a border around elements and space elements in relation to other elements. 

With CSS properties and values, it is possible to apply specific styles to each of these elements, and change the way they behave and/or display on the page. 

The image below illustrates the box model:

![box model](https://lh5.googleusercontent.com/6h6eknil4Xy9Co3cyNgFVRYdW4GE7hKnUuj77BePwZbylXUsaFKxRiqgQwUuWg534OPNsH2_pksTn-N-onhSMkTPojittQvlW6PxY5H-v_JyOk_OHZUxgUoCOg)


What do these different layers mean, and how are they relating to one another?

* **Margin** - Clears an area around the border. The margin does not have a background color, it is completely transparent

* **Border** - A border that goes around the padding and content. The border is affected by the background color of the box

* **Padding** - Clears an area around the content. The padding is affected by the background color of the box

* **Content** - The content of the box, where text and images appear


####Display: block / inline / inline-block

We can display our elements in several different ways. One CSS property, "display", can take on several values that help us in this process: "block", "inline", and "inline-block". 

* An inline element has no line break before or after it, and it tolerates HTML elements next to it.

* A block element has some whitespace above and below it and does not tolerate any HTML elements next to it.

* An inline-block element is placed as an inline element (on the same line as adjacent content), but it behaves as a block element.

To illustrate this, let's look at a few `<div style="width: 50px" ...>` elements, with different display properties: 


![css display examples](https://lh6.googleusercontent.com/E_A8PswxSoUfZixN7FKJhsbGcO-uNeZY9yOXZgVboL14v499L9yH7GOKxezp6Z4t9ocGyOolXVwtxcMm_V03SD05wC1YDm8bg2B3xF7V_MF08qB5aT1VJvMgHg)


####Other positioning tools

Another CSS property, "position", can take "*relative*" or "*absolute*" values, among others. 

A page element with "relative positioning" gives you the control to "absolutely position" children elements inside of it. This might not be obvious to everyone - that's probably because this isn't intuitive. At all. Let's look at an example.

![css position relative](https://lh5.googleusercontent.com/1Rg2JABratNT6dc_ykEeZWH3BRhbgwI7JOl45jeqE-IxPUq2bx_xY_jYJQHqi38czuuQIGUqArhfZndC2SOtube_qDZe5h0wWz2xRlRNX-uiwjOfvz2K-Ld7Iw)


The relative positioning on the parent is what matters here. This what would happen if we forgot that:

![](https://lh3.googleusercontent.com/w2-DIT10v5D-5l5BQPnwBp5nmcLhKvfDeJfU-vYpx1l_73dnanrvsDoN0Z4cobGmEv1N5WEEo8XZ5WrK6UU9q87Iwi1AT9WgFeBy6qD8-AG2VrNLHaDmqTPsXA)

In this small example, it doesn't seem to matter much. But it really is a significant change.

⇒ The "absolutely positioned" elements are positioning themselves in relation to the body element, instead of their direct parent. So if the browser window grows, that element in the bottom left is going to stick with the browser window, not hang back inside, like it was the case in the previous example.


**Static Positioning**

HTML elements are positioned static by default. A "static positioned" element is always positioned according to the normal flow of the page.

Static positioned elements are not affected by the top, bottom, left, and right properties.

**Fixed Positioning**

An element with fixed position is positioned relative to the browser window.

It will not move even if the window is scrolled.

**Float**

The float property specifies whether or not a box (or an element) should float. Note that "absolutely positioned" elements ignore the float property.

Floated elements remain a part of the flow of the web page. This is distinctly different than page elements that use absolute positioning. "Absolutely positioned" page elements are removed from the flow of the webpage; they will not affect the position of other elements and other elements will not affect them, whether they touch each other or not.

There are four valid values for the float property. "Left" and "right" float elements those directions respectively. "None" (the default) ensures the element will not float and "inherit" which will assume the float value from that elements parent element.


## ADDITIONAL RESOURCES

CSS:

* Intro to CSS: <https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Getting_started>
* CSS Box Model: <http://www.w3schools.com/css/css_boxmodel.asp>
