Install brew (assuming that you have Xcode installed).

    $ ruby -e "$(curl -fsS http://gist.github.com/raw/436471/install_homebrew.rb)"

Install git, bash completion and ghost.

    $ brew install git
    $ brew install bash-completion
    $ gem install ghost

Get the Good Old bin package and add it to your profile:

    $ mkdir ~/bin
    $ cd ~/bin
    $ git clone http://github.com/goodold/goodold-bin.git
    $ echo "source \$HOME/bin/goodold-bin/goodold_profile" >> .bash_profile

...or, if you already have it, run a update:

    $ goodold-selfupdate

Start a new shell or reload your profile to get the correct paths:

    $ source .bash_profile

Turn your homebrew dir into a git working directory:

    $ cd /usr/local
    $ git init
    $ git remote add origin git://github.com/mxcl/homebrew.git
    $ git pull origin master

Install mysql and follow the instructions:

    $ brew install mysql

Get the necessary recipes for php52:

    $ git remote add boztek git://github.com/boztek/homebrew.git && git fetch boztek
    $ git checkout boztek/php52 && git checkout -b working
    $ git pull --rebase origin master

Install php:

    $ brew install php52 --with-mysql --with-apache
    $ sudo ln -s /usr/local/Cellar/php52/5.2.13/libexec/apache2/libphp5.so /usr/libexec/apache2/libphp5.2.so

Open the apache config:

    $ mate /etc/apache2/httpd.conf

...and add the following:

    # add this line with the other load module directives
    LoadModule php5_module        libexec/apache2/libphp5.2.so

    # add this at the end of the file
    Include /Users/hugowett/Projects/apache.conf

Create the apache.conf file with the following contents

    
    # get the server name from the Host: header
    UseCanonicalName Off

    # include the server name in the filenames used to satisfy requests
    VirtualDocumentRoot /Users/hugowett/Projects/%0/public_html
    <Directory "/Users/hugowett/Projects">
      AllowOverride All
      Order allow,deny
      Allow from all
    </Directory>

...and restart.

    $ sudo apachectl graceful

Now you should have a working XAMP stack. Just add a "domain.local" directory to ~/Projects with a public_html dir inside and add the domain using `$ ghost add domain.local` and it will be available as a local site.