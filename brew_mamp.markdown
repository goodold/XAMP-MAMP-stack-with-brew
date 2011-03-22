**For Brew driven MAMP with Apache2, PHP 5.3 and MySQL 5.1**

_If you have MacPorts install - you probably should move it to avoid any unintentional conflicts._

Install brew (assuming that you have Xcode installed).

    $ ruby -e "$(curl -fsSLk https://gist.github.com/raw/323731/install_homebrew.rb)"

Install git, bash completion and ghost.
(Perhaps by first following gem instructions at http://wiki.github.com/mxcl/homebrew/gems-eggs-and-perl-modules)

    $ brew install git
    $ brew install bash-completion
    $ sudo gem install ghost

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

    $ brew update

Install mysql and follow the instructions:

    $ brew install mysql

Open the apache config:

    $ mate /etc/apache2/httpd.conf

...and add the following:

    # get the server name from the Host: header
    UseCanonicalName Off

    # include the server name in the filenames used to satisfy requests
    VirtualDocumentRoot /Users/hugowett/Projects/%0/public_html
    <Directory "/Users/hugowett/Projects">
      Options FollowSymLinks
      AllowOverride All
      Order allow,deny
      Allow from all
    </Directory>

...uncomment the following

    LoadModule php5_module        libexec/apache2/libphp5.so

...and maybe comment out the following

    # LoadModule bonjour_module     libexec/apache2/mod_bonjour.so

If you're running Mac OS X 10.6.5 you need to fix your broken apachectl file, open it

    $ mate /usr/sbin/apachectl

...and find and replace

    ULIMIT_MAX_FILES="ulimit -S -n `ulimit -H -n`"

...with

    ULIMIT_MAX_FILES=""

...and restart.

    $ sudo apachectl graceful

Now you should have a working MAMP stack. Just add a "domain.local" directory to ~/Projects with a public_html dir inside and add the domain using `$ ghost add domain.local` and it will be available as a local site.