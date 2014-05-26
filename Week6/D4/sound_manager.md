# SoundManager

- So we've seen some native JavaScript, and we've seen jQuery, which lets us do a bunch of stuff more easily. 
- Remember how we said that jQuery is just a library? SoundManager is just a library. 
  - It's probably overkill for what we're going to do today, but it's a fun example of working with other libraries. 
  - And it runs locally, so we don't have to worry about rate limiting. 

- So what is Sound Manager? 
  - It's a library that lets us work with sound files. 
  - We can do some of this with native HTML5 now, but...
    - SoundManager is more powerful
    - And SoundManager will automatically (and transparently!) use Flash as a fallback if it's not available. 



# Codealong

- Create a new directory  

`mkdir MusicPlayer`

cd into MusicPlayer and create a main.rb

`touch main.rb`

- And create a public directory, with a js subdir and a swf subdir. 

`mkdir public`

`mkdir public/js public/swf `

- Browsers have a security policy that stops files being loaded off the hard disk sometimes, so we're going to have an empty Sinatra app so all of our content. 

- `echo "require 'sinatra'" > main.rb`


- Let's download jQuery and [soundmanager](http://www.schillmania.com/projects/soundmanager2/doc/download/#latest).
  - jQuery goes in our public/js folder. 
  - soundManager/script/soundmanager.js goes in public/js
  - All the files from soundManager/swf/ go in public/swf

- Set up a basic index.html

```
<html>
<head>
  <title>My awsome MP3 Player</title>
  <style type="text/css">
    p { font-size: 400% }
  </style>
</head>
<body>
  <h1>This will be my music player</h1>
  <!-- &#9654; creates a black triangle -->
  <p>&#9654;</p>
</body>
</html>
```

### Now we must find a mp3 file and put in into our public

- now create a music.js in public/js
-Lets Make the onready method play a track

```
soundManager.setup({
  url: '/swf/',
  preferFlash: true,
  onready: function() {
    var mySound = soundManager.createSound({
      id: 'myFirstSound',
      url: '/music_file_name.mp3'
    });
    mySound.play();
  }
})
```

- we shall now need to include soundmanager.js and music.js in index.html

```
<html>
<head>
  <title>My awsome MP3 Player</title>
  <script src="/js/soundmanager2.js"></script>
  <script src="js/jquery-1.11.0.min.js"></script>
  <script src="js/music.js"></script>
  <style type="text/css">
    p { font-size: 400% }
  </style>
</head>
<body>
  <h1>This will be my music player</h1>
  <!-- &#9654; creates a black triangle -->
  <p>&#9654;</p>
</body>
</html> 
```

### Lets hook up our play button. 

comment out all the current code in music.js (or delete it).

lets put our code into a module

```
var myPlayer = myPlayer || {};
```

```
myPlayer.play = function() {
    var song = myPlayer.getSong();
    song.play()
};

```

```
myPlayer.getSong = function() {
  var song = soundManager.createSound({
    id: "myFirstSound",
    url: '/Archers.mp3'
  });
  return song;
}
```

```
myPlayer.setup = function() {
  $('#playbutton').click(function(ev){ myPlayer.play(); });
};

```

```
$(document).ready(function(){
  soundManager.setup({ url: '/swf/', onready: myPlayer.setup });
}/)
```

#### cola on keyboard ....


```
var myPlayer = myPlayer || {};

myPlayer.play = function() {
    var song = myPlayer.getSong();
    var $playbutton = $('#playbutton');
    $playbutton.text('||'); //update play button with pause button

    if ($playbutton.data('state') == 'stopped') { //Play if stopped, ptherwise resume
      song.play();
    } else if ($playbutton.data('state') == 'paused') {
      song.resume();
    }
    // keep my app state in sync
    $playbutton.data('state', 'playing');
};

myPlayer.pause = function(){
  // Pause whatever song is currently playing
  var song = myPlayer.getSong();
  song_pause();
  $('playbutton').text('&#9654').data('state', 'paused'); //update symbol and state
};

myPlayer.getSong = function() {
  var song = soundManager.createSound({
    id: "myFirstSound",
    multiShot: false,
    url: '/Archers.mp3'
  });
  return song;
};


myPlayer.setup = function() {
  var $playbutton = $('#playbutton');
  $playbutton.click(function(ev){ myPlayer.play(); });
  $playbutton.data('state', 'stopped');
};


$(document).ready(function(){
  soundManager.setup({ url: '/swf/', onready: myPlayer.setup });
})


```

- Play and pause. 

```
var myPlayer = myPlayer || {};

myPlayer.play = function() {
    var song = myPlayer.getSong();
    var $playbutton = $('#playbutton');

    $playbutton.html('&#10074 &#10074'); //update play button with pause button

    if ($playbutton.data('state') == 'stopped') { //Play if stopped, ptherwise resume
      song.play();
    } else if ($playbutton.data('state') == 'paused') {
      song.resume();
    }
    // keep my app state in sync
    $playbutton.data('state', 'playing');
};

myPlayer.pause = function(){
  // Pause whatever song is currently playing
  var song = myPlayer.getSong();
  song.pause();
  $('#playbutton').html('&#9654;').data('state', 'paused'); //update symbol and state
};


myPlayer._currentSong = null;
myPlayer.getSong = function() {
  if (!myPlayer._currentSong) {
    myPlayer._currentSong = soundManager.createSound({
      id: "myFirstSound",
      multiShot: false,
      url: '/Archers.mp3'
    });
  }
  return myPlayer._currentSong;
};

myPlayer.playClickHandler = function(ev) {
  if ($('#playbutton').data('state') == 'playing') {
    myPlayer.pause();
  } else {
    myPlayer.play();
  }
}


myPlayer.setup = function() {
  var $playbutton = $('#playbutton');
  $playbutton.click(myPlayer.playClickHandler);
  $playbutton.data('state', 'stopped');
};


$(document).ready(function(){
  soundManager.setup({ url: '/swf/', onready: myPlayer.setup });
})

```

### now we have a working play/pause button


## Lab: iDaft
- Hey, check this thing out: <http://www.najle.com/idaft/>
- But it's in Flash! 
- Flash is SOOOOO 2007. 
- Surely we know enough to do this in JavaScript now? 
- Go for it. 