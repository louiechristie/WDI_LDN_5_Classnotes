# Google Maps

Create a index.html

```
  <html>
  <head>
    <title>Google Maps Demo</title>
  </head>
  <body>
    <h1>General Assembly London</h1>
    <p>Find us during working hours at:</p>
    <address>
      9 Back Hill <br>
      Clerkenwell <br>
      London  <br>
      EC1R 5EN
    </address>
  </body>
  </html>
```


To get google maps on our webpage.

Go to google maps console.
<https://cloud.google.com/console/project>

- Create a new Project
- Then go to **APIs & auth**/APIs
- Turn on Google Maps JavaScript API v3
- go to **Credentials**
- **Create New Key** then choose **Browser**


Under API Access, create a new API key.

Put this into your webpage  
`<script type="text/javascript"src="https://maps.googleapis.com/maps/api/js?key=API_KEY&sensor=FALSE"></script>`

We need to add the API Key into the script for example

API KEY example = **AIzsfgyCx6XWfnk--abcdddHBxbPrRDJSHJhM7Sc**    

`<script type="text/javascript"src="https://maps.googleapis.com/maps/api/js?key=AIzsfgyCx6XWfnk--abcdddHBxbPrRDJSHJhM7Sc&sensor=TRUE_OR_FALSE"></script>`

and set the **sensor=** to false  

`<script type="text/javascript"src="https://maps.googleapis.com/maps/api/js?key=AIzsfgyCx6XWfnk--abcdddHBxbPrRDJSHJhM7Sc&sensor=false"></script>`

We need to add a container for our google map.
And add a stylesheet

```
  <html>
  <head>
    <title>Google Maps Demo</title>
    <link rel="stylesheet" type="text/css" href="style.css">
    <script type="text/javascript"src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCx6XWfnk--JbccokHBxbPrRDJSHJhM7Sc&sensor=false"></script>
  </head>
  <body>
    <h1>General Assembly London</h1>
    <p>Find us during working hours at:</p>
    <div id="map_canvas"></div>
    <address>
      9 Back Hill <br>
      Clerkenwell <br>
      London  <br>
      EC1R 5EN
    </address>
  </body>
  </html>
```

Add a style.css into our directory.

**CSS**  

```
#map_canvas {
  display: inline-block;
  width: 300px;
  height: 300px;
  background-color: red;
  float: left;
  margin-right: 5px;
}
```

We should now have a red rectangle in our page.

- Create a js file `application.js`  
- Include it **after** google maps

- In application.js 



```
function initialise(){
}
google.maps.event.addDomListener(window, 'load', initialise); 
```


```
function initialise() {
  var mapOptions = {
    center: new google.maps.LatLng(51.5, -0.1),
    zoom: 14
  };
  var map = new google.maps.Map(document.getElementById('map_canvas'), mapOptions);
};

google.maps.event.addDomListener(window, 'load', initialise);
```

Now we can update our map and remove some of the options for the user

```
function initialise() {
  var mapOptions = {
    center: new google.maps.LatLng(51.5, -0.1),
    zoom: 14,
    streetViewControl: false,
    mapTypeControl: false
  };
  var map = new google.maps.Map(document.getElementById('map_canvas'), mapOptions);
};

google.maps.event.addDomListener(window, 'load', initialise);
```

Now we should add a marker so that our user can see exactly where we are.

```
function initialise() {
  var mapOptions = {
    center: new google.maps.LatLng(51.5, -0.1),
    zoom: 14,
    streetViewControl: false,
    mapTypeControl: false
  };
  var map = new google.maps.Map(document.getElementById('map_canvas'), mapOptions);
  addMarker(map);
};


function addMarker(map){
  var position = new google.maps.LatLng(51.522, -0.1095);
  var marker = new google.maps.Marker({
      position: position,
      map: map,
      title: "GA London"
  });
  map.setCenter(position);
}

google.maps.event.addDomListener(window, 'load', initialise);
```

- We now need to Add Jquery into our directory and include it into our index.html


Comment out our old **addMarker** function

And we are going to create a new addMarker function with a geocoder

```
function addMarker(map) {
  console.log("Showing marker from geocoder results.");
  var geocoder = new google.maps.Geocoder();
  var showMarkerFromGeocoderResults = function(results, status) {
    if (status == google.maps.GeocoderStatus.OK) {
      var marker = new google.maps.Marker({
          position: results[0].geometry.location,
          map: map
      });
    } else {
      console.warn("coulnt geocod address.");
    }
  }
  $('address').each(function(i, el) {
    var geocoderOptions = { address: $(el).text() };
    geocoder.geocode(geocoderOptions, showMarkerFromGeocoderResults);
  });
}
```


Now lets center the map to the geo location  
`map.setCenter(results[0].geometry.location);`


```
function addMarker(map) {
  console.log("Showing marker from geocoder results.");
  var geocoder = new google.maps.Geocoder();
  var showMarkerFromGeocoderResults = function(results, status) {
    if (status == google.maps.GeocoderStatus.OK) {
      var marker = new google.maps.Marker({
          position: results[0].geometry.location,
          map: map
      });
      map.setCenter(results[0].geometry.location);
    } else {
      console.warn("coulnt geocod address.");
    }
  }
  $('address').each(function(i, el) {
    var geocoderOptions = { address: $(el).text() };
    geocoder.geocode(geocoderOptions, showMarkerFromGeocoderResults);
  });
}
```

We now have a google map that will center on the GeoLocation of our address.

Add a new address to index.html 

```
  <address>
    Karen House
    1-11 Bache's Street
    London
    N1 6DL
  </address>
```


















