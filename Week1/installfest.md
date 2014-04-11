# Installfest

### Pre-install
1. Identify which version of OSX you’re using - ideally you should have Mavericks or newer (10.9.2)
2. Ensure you’ve got Xcode installed - https://itunes.apple.com/us/app/xcode/id497799835?ls=1&mt=12
3. Ensure that you’ve uninstalled any antivirus software you may have, as it can prevent some of the tools from installing properly

### Install and connect to HipChat
1. Sign up to [HipChat](https://www.hipchat.com/)
2. Install the OSX app and keep it running

### Install Google Chrome
1. Go to https://google.com/chrome
2. Click on Download Chrome
3. Go to the Downloads folder and run the googlechrome.dmg package

### Install Command Line Tools
1. Run from the command line: `xcode-select --install`
2. Choose `install tools` from the prompt and `agree` to the terms
3. If you recieve a message saying “Can’t install the software because it is not currently available from the Software Update server”... it’s probably because the command line tools are already installed...

### Install Sublime Text 2
1. Download and install [Sublime Text 2](http://www.sublimetext.com/) - version 2.0.2 is current
2. Make a symlink for Sublime Text, allowing us to launch it from the command line:
`sudo ln -s /Applications/Sublime\ Text\ 2.app/Contents/SharedSupport/bin/subl /bin/subl`
3. Install the [sublime package manager](https://sublime.wbond.net/installation#st2)

### Install Package Manager
1. The package manager allows us to install and update software (like Ruby, Git, MongoDB, etc) from the command line:
2. Open http://brew.sh/, scroll down to Install Homebrew and copy+paste the command into Terminal.app
3. Ensure that the Homebrew directories are in your PATH:
`echo "export PATH=/usr/local/bin:$PATH" >> ~/.bash_profile`
4. Reload Bash profile file:
`source ~/.bash_profile`
5. Ensure that Homebrew is raring to brew and fix any issues:
6. brew doctor
7. Update Homebrew:
`brew update`


### Install Ruby Environment (Rbenv) and builder
1. Install rbenv (the Ruby version manager) and ruby-build (the Ruby version builder) from Homebrew:
`brew install ruby-build rbenv`
2. Ensure that rbenv is loaded whenever we open a command line session:
`echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bashrc`
3. Move the rbenv environment higher in the load order:
`echo 'export PATH=$HOME/.rbenv/shims:$PATH' >> ~/.bashrc`
4. Quit the current terminal (Cmd+Q)
5. And then reopen the terminal application, ensuring there are no errors

### Install a version of Ruby with ruby-build
1. OS X 10.8 comes with Ruby baked in, but it’s version 1.8.7 which is quite old. We could upgrade that version of Ruby to a newer one, but what if we needed to run one version of Ruby for one app, and a different version for another?
2. Install Ruby 2.0.0 patch level 247. This is the latest version of Ruby 2.0:
`rbenv install 2.0.0-p247`
3. This is required every time we install a new Ruby or install a gem with a binary:
`rbenv rehash`
3. Set the global Ruby to the one we’ve just installed, which is a sensible default:
`rbenv global 2.0.0-p247`
4. Test you have the right version with `ruby -v`

### Skip gem rdoc generation
1. Whenever we install a gem, it also installs a bunch of documentation we probably don’t want - the following commans allows us to avoid this:
`echo 'gem: --no-rdoc --no-ri' >> ~/.gemrc`

### Install Bundler
1. Bundler manages Ruby gems per-project/application, and makes it trivial to install all the dependencies for an application:
`gem install bundler`
`rbenv rehash` (because we’ve installed a new binary)

### Install Git
1. This ensures we can upgrade Git more easily:
`brew install git`

### Customise Bash

1. Enable colours for things that care about it (eg. ls)
`echo 'export CLICOLOR=1' >> ~/.bashrc`
2. Use less as your pager:
`echo 'export PAGER=less' >> ~/.bashrc`
3. Don't use pager when tab completing things on the commandline (just list them all)
`echo 'set page-completions off' >> ~/.inputrc`
4. Set sublime as the default editor:
`echo "export EDITOR='subl -w -n'" >> ~/.bashrc`
5. Make more things bash completable (git commands and files etc)
  - `brew install bash-completion`
  - Add this to .bashrc (you probably want to hipchat it to them)
  ```
    if [ -f $(brew --prefix)/etc/bash_completion ]; then
      . $(brew --prefix)/etc/bash_completion
    fi
    ```
6. Load the git-prompt in your .bashrc: 
```
  if [ -f $(brew --prefix)/etc/bash_completion.d/git-prompt.sh ]; then
    . $(brew --prefix)/etc/bash_completion.d/git-prompt.sh
  fi
```
7. Customise your PS1 variable to be friendlier/more useful: 
`echo "export PS1='\$(__git_ps1 \"(%s) \") \W \$ '" >> ~/.bashrc`
8. Make git-prompt show whether the branch is dirty or not:
`echo "export GIT_PS1_SHOWDIRTYSTATE=1" >> ~/.bashrc`
9. set up to ignore case sensitivity
`echo "set completion-ignore-case on" >> ~/.inputrc`
10. tolerate hyphens and underscores too 
`echo "set completion-map-case on" >> ~/.inputrc`
11. make symlinks look ‘better’
`echo "set mark-symlinked-directories on" >> ~/.inputrc`
12. show ambiguous files/directories to choose
`echo "set show-all-if-ambiguous on" >> ~/.inputrc`
13. ensure bash_profile reads the bashrc file
`echo "source ~/.bashrc" >> ~/.bash_profile`

  
### Install Wget
1. We will often download files from the internet in the command line… this is the tool to do that:
`brew install wget`

### Install PostgreSQL
1. Download [PostgreSQL](http://postgresapp.com/)
2. Unzip the downloaded file and drag to `applications`
3. Choose `automatically start at login`
