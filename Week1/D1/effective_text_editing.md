## Effective Text Editing with SublimeText

Developers spend a large portion of their time writing computer programs. Computer programs are just text files with instructions written in a special syntax i.e. a programming language.

We create and edit these text files using text editor (e.g. notepad or TextEdit) but there are features and functionality that can be added to text editors that can make our job as developers much easier.

## IDEs vs Text Editors

An IDE is an all-singing, all-dancing, program that provides everything you need to create, edit, test and deploy a software project - all in one place. Popular examples of IDEs are Visual Studio, Eclipse, RubyMine.

Text Editors designed for programming (like Sublime) are much more limited in their scope, focussing on the job of helping your write good code. Because of this, you must also be comfortable with the command line.

Some programming languages are rarely used outside of an IDE (e.g. Visual Basic). Some developers prefer IDEs, some prefer Text Editors. Sometimes a company might ask you to use their tool of choice, sometimes they might let you use whatever your want to.

For Ruby and Ruby on Rails it is very common to just use a text editor, although there are IDEs that support Rails. Either way, it is better to learn how to code using just a text editor & command line as this way you learn all the fundamental concepts that a 'magical' IDE can hide from you.


##SUBLIME TEXT

Sublime Text is:

* multi-platform (OS X, Windows, Linux)
* popular (widely used for web development)
* free to try (though you will be nagged to purchase it)
* extensible (we can add functionality via plugins)

###Sublime Text Basics

Sublime will be the software we spend the most time with. Every time you write code, this will be in Sublime.

### Launching Sublime

To open sublime, simply click the icon in the Dash or Launchpad. Or just type `subl` in a terminal. This last approach works because you created a symbolic link to the program's binary during the installfest.

To open Sublime with a specific file, we can use the command line again, but this time passing in a file name:

```
subl kaiser_chiefs/index.html
```

#### Working with more than one file

If we want to work on another file we can use the `file/open`, or we can just use the `subl` command again:

```
subl kaiser_chiefs/index.md
```
Notice that the file is opened in the same instance of Sublime as a separate tab. We can display these files side-by-side by selecting `view/layout/columns 2` and then dragging the tab for one of the files over to the other column.

Another way to see what files we have open is to display the **sidebar**. Turn this on by going to `view/side bar/show side bar`. This shows you which files are open in each column (or group). We can also rearrange the file tabs by dragging the corresponding entries in the sidebar between groups.


#### Project mode

Real-world software projects often involve _many_ files organised into folders. It is handy to be able to see all the files in our project when working in our text editor. Sublime makes this easy as it supports a project mode. To use this we simply pass a directory instead of a file:

```
subl kaiser_chiefs
```
or...

```
cd kaiser_chiefs
subl .
```

Notice that the sidebar now has a folders section that shows all the files and folders in the project. Clicking on a folder expands the view to show its contents.

### Settings and themes

`cmd + ,` allows you to access the sublime's preferences.

It opens this file as a JSON object (we will learn all about JSON in the next few weeks). 
It basically presents the settings as a series of keys and values - you can add keys/values, and/or modify the existing values to fit your personal preferences.

For now, add the following:

```
"find_selected_text": true,
"font_face": "Monaco",
"tab_size": 2,
"translate_tabs_to_spaces": true
```

We can change the colour scheme sublime uses by going to "sublime text 2/preferences/color scheme" and selecting one. I recommend "dawn". When you select a scheme it changes all the syntax highlighting colours.


### Packages

Sublime works with a lot of plugins, and we will install new plugins every week. Before, you had to download the package manually and add it to Sublime's plugins folder. Now there is a package manager for Sublime, which works a bit like brew; you ask for a package and sublime will install it for you.

In short, Sublime is highly customisable, you can play around by editing your user settings and by installing plugins. We will do this using **Package Control**. 

* To install the package manager, press `ctrl + ``  
* then copy/paste this command (yes, the whole thing!) into the newly opened Sublime terminal: 

[Package Control installation page](https://sublime.wbond.net/installation#st2)

```
import urllib2,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```

* restart Sublime

#### Adding the SidebarEnhancements Package

* press Cmd + Shift + P , this will open the "command palette", giving you access to a lot of commands.
* write "package control" in the text input and you'll see all the commands that the package control installation added to Sublime
* now select "Package Control: Install Package", wait 2 secs and you'll see another window appearing showing you all the packages you can install with Sublime.
* write "sidebar" and you should see a package called "SidebarEnhancements". Once this package is selected, press "enter" and the prompt at the bottom of the Sublime window should indicate that an installation is running.
* once the installation is completed, you should be able to right-click on a folder or a file inside the sidebar and see a lot of new options .

(SidebarEnhancements is now only for ST3... download file from HipChat and navigate to `preferences - browse packages` and place into the packages folder)

You can install a lot of others themes, plugins, extensions, syntax highlighters, etc. with Package Control - and we will customize Sublime further over the next few weeks.

  - GitGutter


**Find / find and replace**

* If you press `Cmd + f`, a text input should appear at the bottom of the window, giving you the option of finding text in the current document.
* ` Cmd + g` brings you to the next occurrence of the word you're searching for
* `Cmd + shift + g` does the same, but brings you to the previous occurrence
* `Cmd + shift + f` brings you to the search and replace menu
* If you press `Cmd + alt + f`, the window at the bottom should appear with 3 inputs: one for the word to find, one for the path, one for the replace value. If you type `<project>` inside the "path" text input, the search will only be executed within the scope of the current project.


**Sublime settings**

``Cmd + ```, allows you to access the preferences settings.

It opens this file as a JSON object (we will learn all about JSON in the next few weeks). 
It basically presents the settings as a series of keys and values - you can add keys/values, and/or modify the existing values to fit your personal preferences.

For now, add the following:

`"tab_size": 2,`

`"translate_tabs_to_spaces": true`


### Cursor tricks

* Put the cursor before the first character, press "Alt" and drag the cursor at the end the paragraph -> will allow you to enter text before all of the selected lines
* Cmd + cursor click → will set multiple blinking cursors on your file, allowing you to edit multiple elements at once. 

There are several other ways to use your cursor with combinations of "Alt", "Cmd" and "Shift", allowing you to become more productive as you go. 

See [gist.github.com/lucasfais/1207002](https://gist.github.com/lucasfais/1207002) for more examples. Try out a few! 

### Shortcuts

In terminal: 

`subl file1` will open file 1 in Sublime.
 
`subl .` will open current directory in Sublime. 

**In Sublime:**

Keypress          |  Action
------------------|--------- 
cmd + s           |  save changes in current file - DO IT OFTEN!!!
cmd + q           |  close Sublime
cmd + w           |  close one file at a time
cmd + alt + arrow |  switch between files
cmd + numbers     |  also switch between specific files
cmd + f           |  search in you current file
cmd + shift + f   |  search the entire project
cmd + d           |  go to next searched word in file
ctrl + cmd + g    |  selects all searched words in your file.
cmd + w           |  close each window at a time
cmd + shift + p   |  take us to a command palette where we will be given menu options without leaving our keyboard.
cmd + p           |  open the file finder
cmd + 1..0        |  highlight the corresponding tab
cmd + p :NUMBER   |  put the cursor at line number NUMBER
cmd + ,           |  open the preferences
cmd + backspace   |  remove the line before your cursor
cmd + right/left  |  moves your cursor to the end/beginning of the line


## Extras

- Fonts:
  - http://www.tobias-jung.de/seekingprofont/
  - http://sourceforge.net/projects/sourcecodepro.adobe/files/latest/download

[5 things you didn't know sublime text 2 could do](http://www.creativebloq.com/design/5-things-you-didnt-know-sublime-text-2-could-do-1132849)  
[Essential sublime text 2 plugins and extensions](http://net.tutsplus.com/tutorials/tools-and-tips/essential-sublime-text-2-plugins-and-extensions/)


