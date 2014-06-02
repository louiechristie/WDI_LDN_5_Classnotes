# GEM CREATION


Today we will create a gem, and learn how to integrate it within a rails app.


## Creating the gem


First, in terminal:

    $ mkdir create_a_gem
    $ cd create_a_gem
    $ bundle gem hello_world
    ➜  create_a_gem  bundle gem hello_world
          create  hello_world/Gemfile
          create  hello_world/Rakefile
          create  hello_world/LICENSE.txt
          create  hello_world/README.md
          create  hello_world/.gitignore
          create  hello_world/hello_world.gemspec
          create  hello_world/lib/hello_world.rb
          create  hello_world/lib/hello_world/version.rb
    Initializating git repo in /Users/JCFDB/Downloads/w9d4/create_a_gem/hello_world

- - -

Then in Sublime, open "hello_world" → all the code for our gem is in this folder.

We've got a few files in here.

In "hello_world.rb", we can find:

    require "hello_world/version"

    module HelloWorld
      # Your code goes here...
    end

And in "hello_world.gemspec", we have:

    # coding: utf-8
    lib = File.expand_path('../lib', __FILE__)
    $LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
    require 'hello_world/version'

    Gem::Specification.new do |spec|
      spec.name          = "hello_world"
      spec.version       = HelloWorld::VERSION
      spec.authors       = ["Michael Pavling"]
      spec.email         = ["pavling@gmail.com"]
      spec.description   = %q{TODO: Write a gem description}
      spec.summary       = %q{TODO: Write a gem summary}
      spec.homepage      = ""
      spec.license       = "MIT"

      spec.files         = `git ls-files`.split($/)
      spec.executables   = spec.files.grep(%r{^bin/}) { |f| File.basename(f) }
      spec.test_files    = spec.files.grep(%r{^(test|spec|features)/})
      spec.require_paths = ["lib"]

      spec.add_development_dependency "bundler", "~> 1.3"
      spec.add_development_dependency "rake"
    end

- - -

We have a file handling the versioning, in "lib/hello_world/version.rb":

    module HelloWorld
      VERSION = "0.0.1"
    end

→ this should be updated each time we do a new release of our gem.

- - -

We have a few rake tasks already at our disposal. 

In terminal, we can view them by typing:

    $ rake -T
    rake build    # Build hello_world-0.0.1.gem into the pkg directory
    rake install  # Build and install hello_world-0.0.1.gem into system gems
    rake release  # Create tag v0.0.1 and build and push hello_world-0.0.1.gem ...

- - -

Let's edit our "hello_world.gemspec" file:

# coding: utf-8
lib = File.expand_path('../lib', __FILE__)
$LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
require 'hello_world/version'

Gem::Specification.new do |spec|
  spec.name          = "hello_world"
  spec.version       = HelloWorld::VERSION
  spec.authors       = ["Michael Pavling"]
  spec.email         = ["pavling@gmail.com"]
  spec.description   = %q{This gem can say Hello in more than 100 languages}
  spec.summary       = %q{No idea what to put here...}
  spec.homepage      = ""
  spec.license       = "MIT"

  spec.files         = `git ls-files`.split($/)
  spec.executables   = spec.files.grep(%r{^bin/}) { |f| File.basename(f) }
  spec.test_files    = spec.files.grep(%r{^(test|spec|features)/})
  spec.require_paths = ["lib"]

  spec.add_development_dependency "bundler", "~> 1.3"
  spec.add_development_dependency "rake"
end

- - -

First, on the same level as the "lib" folder, create a "resources" folder. In it, add a "hello.yml" file containing the 100+ ways to say "hello" around the world:

    // https://gist.github.com/Pavling/e7598cd641beb8bde778

    :hello:
      :afrikaans : "goeiedag"
      :albanian : "mirë dita" 
      :alsatian : "ponchour"
      :arabic : "salam"
      :armenian : "barev"
      :austrian : "grüß gott" 
      :azeri : "salam"
      :bambara : "ani sogomen" 
      :baoule : "n'dja arè (to a man) / mô arè (to a woman)"
      :basque : "egunon"
      :bassa_menyega : "menyéga bé"
      :belarusian :  "vitaju / dobraj ranicy"
      :bengali : "namaskar"
      :berber:  "ass-iyessbhène"
      :beri_beri : "inda wadou"
      :bete :  "ayo"
      :bobo :  "ka tiana"
      :bosnian : "dobardan"
      :breton :  "demat"
      :bulgarian : "добър ден"
      :burmese : "mingalaba"
      :catalan : "hola"
      :chamorro :  "hafa adai"
      :cherokee :  "osiyo"
      :chinese : "你好"
      :cornish : "de(th) da"
      :corsican :  "bonghjornu"
      :croatian :  "bok"
      :czech : "dobrý den"
      :danish :  "goddag" 
      :dari : "salâm"
      :djerma : "mat ni kani"
      :djoula : "anissoroma"
      :dogon : "alapiale" 
      :dutch : "goede middag"
      :ede_idaasha: "ékooni"
      :english : "hello"
      :esperanto : "bonan tagon" 
      :estonian :  "tere hommikust"
      :fang :  "mbolo"
      :faroese : "hey"
      :fijian :  "yadra"
      :finnish : "hyvää päivää"
      :flemish : "hoeien nuh'nt"
      :french : "bonjour"
      :frisian : "ha"
      :friulan : "bondi"
      :fulani :  "diarama"
      :galician : "ola"
      :gallo : "bonjou"
      :georgian :  "gamarjoba"
      :german :  "hallo"
      :greek : "gia"
      :haitian : "bonjou"
      :hawaiian :  "aloha"
      :hebrew :  "shalom"
      :hindi : "namaste"
      :hungarian : "jó napot kívánok"
      :icelandic : "goðan dagin"
      :igbo: "ndeewo" 
      :indonesian : "selamat pagi" 
      :irish_gaelic :  "dia duit"
      :italian : "ciao" 
      :japanese :  "konnichi wa" 
      :kabylian :  "azul"
      :khmer : "chumreap suor"
      :korean :  "annyǒng hashimnikka" 
      :kurdish : "rojbas "
      :ligurian : "ciâo"
      :lingala : "mbote"
      :lithuanian : "laba diena" 
      :luxembourgeois :  "moien"
      :malay : "selamat pagi"
      :maltese : "merħba"
      :maori : "kia ora"
      :mongolian : "sain baina uu"
      :nepali :  "namaste"
      :norwegian : "god dag"
      :occitan : "bon jorn"
      :persian : "dorood" 
      :polish : "dzień dobry"
      :portuguese : "bom dia"
      :romanian : "bună ziua"
      :russian : "добрый день"
      :scottish_gaelic : "hallo"
      :serbian : "dobar dan"
      :slovak : "dobré ráno" 
      :soninke : "an mou diom"
      :spanish : "buenos días"
      :swahili : "jambo"
      :swedish : "god dag"
      :swiss_german :  "guete tog"
      :tagalog : "kumusta"
      :tahitian : "'ia ora na"
      :tamachek : "wayi wan"
      :tamil : "kallaï vannakam"
      :thai :  "สวัสดีค่ะ" 
      :tibetan : "tashi delek"
      :turkish : "merhaba"
      :ukrainian : "вітаю"
      :vietnamese :  "chào"
      :west_indian_creole :  "bonjou"
      :wolof : "nangadef"
      :zulu:  "sawubona"

- - -

Then, in "hello_world.rb", create a method that retrieves the YAML file and parses it to a hash and returns the value for a given key:

    require "hello_world/version"
    require "yaml"

    module HelloWorld
      def self.hash # TOOD: rename this, as it overloads a Ruby built-in method!
        path = File.expand_path("../../resources/hello.yml", __FILE__)
        @@hello_hash ||= YAML.load_file path
        @@hello_hash[:hello]
      end
    end

- - -

In terminal, we can access the gem console, and see that the hash is available to us:

    $ bundle console
    Resolving dependencies...
    > HelloWorld.hash
    => {:afrikaans=>"goeiedag", :albanian=>"mirë dita", :alsatian=>"ponchour", :arabic=>"salam", :armenian=>"barev", :austrian=>"grüß gott", :azeri=>"salam", :bambara=>"ani sogomen", :baoule=>"n'dja arè (to a man) / mô arè (to a woman)", :basque=>"egunon", :bassa_menyega=>"menyéga bé", :belarusian=>"vitaju / dobraj ranicy", :bengali=>"namaskar", :berber=>"ass-iyessbhène", :beri_beri=>"inda wadou", :bete=>"ayo", :bobo=>"ka tiana", :bosnian=>"dobardan", :breton=>"demat", :bulgarian=>"добър ден", :burmese=>"mingalaba", :catalan=>"hola", :chamorro=>"hafa adai", :cherokee=>"osiyo", :chinese=>"你好", :cornish=>"de(th) da", :corsican=>"bonghjornu", :croatian=>"bok", :czech=>"dobrý den", :danish=>"goddag", :dari=>"salâm", :djerma=>"mat ni kani", :djoula=>"anissoroma", :dogon=>"alapiale", :dutch=>"goede middag", :ede_idaasha=>"ékooni", :english=>"hello", :esperanto=>"bonan tagon", :estonian=>"tere hommikust", :fang=>"mbolo", :faroese=>"hey", :fijian=>"yadra", :finnish=>"hyvää päivää", :flemish=>"hoeien nuh'nt", :french=>"bonjour", :frisian=>"ha", :friulan=>"bondi", :fulani=>"diarama", :galician=>"ola", :gallo=>"bonjou", :georgian=>"gamarjoba", :german=>"hallo", :greek=>"gia", :haitian=>"bonjou", :hawaiian=>"aloha", :hebrew=>"shalom", :hindi=>"namaste", :hungarian=>"jó napot kívánok", :icelandic=>"goðan dagin", :igbo=>"ndeewo", :indonesian=>"selamat pagi", :irish_gaelic=>"dia duit", :italian=>"ciao", :japanese=>"konnichi wa", :kabylian=>"azul", :khmer=>"chumreap suor", :korean=>"annyǒng hashimnikka", :kurdish=>"rojbas ", :ligurian=>"ciâo", :lingala=>"mbote", :lithuanian=>"laba diena", :luxembourgeois=>"moien", :malay=>"selamat pagi", :maltese=>"merħba", :maori=>"kia ora", :mongolian=>"sain baina uu", :nepali=>"namaste", :norwegian=>"god dag", :occitan=>"bon jorn", :persian=>"dorood", :polish=>"dzień dobry", :portuguese=>"bom dia", :romanian=>"bună ziua", :russian=>"добрый день", :scottish_gaelic=>"hallo", :serbian=>"dobar dan", :slovak=>"dobré ráno", :soninke=>"an mou diom", :spanish=>"buenos días", :swahili=>"jambo", :swedish=>"god dag", :swiss_german=>"guete tog", :tagalog=>"kumusta", :tahitian=>"'ia ora na", :tamachek=>"wayi wan", :tamil=>"kallaï vannakam", :thai=>"สวัสดีค่ะ", :tibetan=>"tashi delek", :turkish=>"merhaba", :ukrainian=>"вітаю", :vietnamese=>"chào", :west_indian_creole=>"bonjou", :wolof=>"nangadef", :zulu=>"sawubona"}

- - -

We can now expand our "hello_world.rb":

    require "hello_world/version"
    require "yaml"

    module HelloWorld
      def self.say_hello(language=nil, name=nil)
        raise ArgumentError, "You need to provide a language key" if language.nil?
        [hash[language.to_sym], name].compact.join(" ")
      end

      def self.hash
        path = File.expand_path("../../resources/hello.yml", __FILE__)
        @@hello_hash ||= YAML.load_file path
        @@hello_hash[:hello]
      end
    end

Let's test this in terminal:

    $ bundle console
    > HelloWorld.say_hello
    ArgumentError: You need to provide a language key

→ we're getting an error, as expected, because we didn't provide the language key.

> HelloWorld.say_hello :swahili, "Dave"
=> "jambo Dave"

It works!

- - -

Back in "hello_world.rb", create a method that :

    require "hello_world/version"
    require "yaml"

    module HelloWorld
      def self.say_hello(language=nil, name=nil)
        raise ArgumentError, "You need to provide a language key" if language.nil?
        [hash[language.to_sym], name].compact.join(" ")
      end

      def self.languages
        hash.keys
      end

      def self.random
        key = languagessubl.sample
        hash.assoc key
      end
      
      def self.hash
        path = File.expand_path("../../resources/hello.yml", __FILE__)
        @@hello_hash ||= YAML.load_file path
        @@hello_hash[:hello]
      end
    end

Let's test these new functions in the gem console:

    $ bundle console
    > HelloWorld.languages
    => [:afrikaans, :albanian, :alsatian, :arabic, :armenian, :austrian, :azeri, :bambara, :baoule, :basque, :bassa_menyega, :belarusian, :bengali, :berber, :beri_beri, :bete, :bobo, :bosnian, :breton, :bulgarian, :burmese, :catalan, :chamorro, :cherokee, :chinese, :cornish, :corsican, :croatian, :czech, :danish, :dari, :djerma, :djoula, :dogon, :dutch, :ede_idaasha, :english, :esperanto, :estonian, :fang, :faroese, :fijian, :finnish, :flemish, :french, :frisian, :friulan, :fulani, :galician, :gallo, :georgian, :german, :greek, :haitian, :hawaiian, :hebrew, :hindi, :hungarian, :icelandic, :igbo, :indonesian, :irish_gaelic, :italian, :japanese, :kabylian, :khmer, :korean, :kurdish, :ligurian, :lingala, :lithuanian, :luxembourgeois, :malay, :maltese, :maori, :mongolian, :nepali, :norwegian, :occitan, :persian, :polish, :portuguese, :romanian, :russian, :scottish_gaelic, :serbian, :slovak, :soninke, :spanish, :swahili, :swedish, :swiss_german, :tagalog, :tahitian, :tamachek, :tamil, :thai, :tibetan, :turkish, :ukrainian, :vietnamese, :west_indian_creole, :wolof, :zulu]
    > HelloWorld.random
    => [:chinese, "你好"]


Integrating into a Rails project.


Inside the "create_a_gem" folder, create a new rails project

    $ rails new my_hello_gem

- - -

Then, to our Gemfile, add:

    gem "haml-rails"
    gem 'hello_world', path: "YOUR_OWN_PATH/hello_world"

→ to find "YOUR_OWN_PATH", cd into the "create_a_gem" in terminal, and type  pwd , then copy/paste the resulting path in lieu of "YOUR_OWN_PATH".

- - -

To pull a gem from github, you could also do:
 
    gem "random_name", git: "git@github.com/gemguy/gem.git"
 
 ...or:
    
    gem "random_name", github: "gemguy/gemname/"

- - -

Then, in terminal:

    $ bundle
    $ rails g controller home index

→ we now have a "home" controller with an index function.

In "home_controller.rb":

    class HomeController < ApplicationController
      def index
        @hello = HelloWorld.random
      end
    end

And in "views/home/index.html.haml":

    %center
      %h1= "#{@hello[1]} (#{@hello[0]})"

- - -

We can start our server, and visit "http://localhost:3000/home/index"...

Behold - our first gem! → "Hello World!"



- - -


Our gem can do *anything* we like. For instance, we can change the return value of the `random` method to be an object structure rather than an array:

    HelloTranslation = Struct.new(:language, :hello) # place after module definition

    HelloTranslation.new(*hash.assoc(key)) # replace the `hash.assoc key` line

And use it in the view thus:

    <%= "#{@hello.language} (#{@hello.hello})" %>








