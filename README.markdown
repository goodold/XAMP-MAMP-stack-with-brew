# XAMP/MAMP stack with brew

**For Brew driven MAMP with Apache2, PHP 5.3 and MySQL 5.1**

_If you have MacPorts install - you probably should move it to avoid any unintentional conflicts._

Install brew (assuming that you have Xcode installed).

    $ ruby -e "$(curl -fsSLk https://gist.github.com/raw/323731/install_homebrew.rb)"

Install git and bash completion.

    $ brew install git
    $ brew install bash-completion

Install dnsmasq for local wildcard domain.

    $ brew install dnsmasq
    $ cp /usr/local/Cellar/dnsmasq/*/dnsmasq.conf.example /usr/local/etc/dnsmasq.conf
    $ sudo cp /usr/local/Cellar/dnsmasq/2.57/uk.org.thekelleys.dnsmasq.plist /Library/LaunchDaemons
    $ sudo launchctl load -w /Library/LaunchDaemons/uk.org.thekelleys.dnsmasq.plist

Edit your dnsmasq.conf and add an entry for your local development domain. At Good Old we generally use the develpers first name or nick as top level domain. That way we can check out each others work without having to change the IP for *.dev or a similarly generic domain. For me that would be:

    address=/.hugo/127.0.0.1

You can also add your colleagues like this:

    address=/.simme/192.168.4.123

Restart dnsmasq to let the configuration changes take effect.

    $ sudo launchctl stop uk.org.thekelleys.dnsmasq
    $ sudo launchctl start uk.org.thekelleys.dnsmasq

Install mysql and follow the instructions:

    $ brew install mysql

Open the apache config:

    $ mate /etc/apache2/httpd.conf

...uncomment the following

    LoadModule php5_module        libexec/apache2/libphp5.so

...and add the following:

    # add this at the end of the file
    Include /Users/hugowett/Projects/apache.conf

Create the apache.conf file with the following contents

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

...and restart.

    $ sudo apachectl graceful

Now you should have a working XAMP stack. Just add a "domain.hugo" directory to ~/Projects with a public_html dir inside and it will be available as a local site.

## Configuring xhprof

### 1. Download and install latest version:

```bash
cd ~/Projects
git clone https://github.com/facebook/xhprof.git xhprof.local xhprof.local
cd xhprof.local/extension
phpize
CFLAGS="-arch x86_64 -arch i386"
./configure
make
sudo make install
```

There is an issue with timers, see https://github.com/facebook/xhprof/commit/b7b4ad7d1b72803fa8235e988d6cbf93d80ed59d#L2R276
You can confirm this by running:

```bash
make test
```

Make the web interface available by aliasing it to public_html:

````bash
cd ..
ln -s xhprof_html public_html
```

### 2. Now add the xhprof extension to /etc/php.ini:

```ini
[xhprof]
extension=xhprof.so
xhprof.output_dir=/private/tmp
```

### 3. Restart Apache and double-check that the xhprof extension is properly loading -
```bash
sudo apachectl restart
php -m | grep xhprof
```

### 4. Add this to devel settings (replace USER with your username):

xhprof directory:

    /Users/USER/Projects/xhprof.local

XHProf URL:

    http://xhprof.local

### 5. To be able to generate callgraphs:

```bash
brew install graphviz
```

See http://xhprof.local/docs for documentation on using XHProf

## Installing Good Old Bin

This is an optional step: get the Good Old bin package and add it to your profile:

    $ mkdir ~/bin
    $ cd ~/bin
    $ git clone http://github.com/goodold/goodold-bin.git
    $ echo "source \$HOME/bin/goodold-bin/goodold_profile" >> ~/.bash_profile

...or, if you already have it, run a update:

    $ ~/bin/goodold-bin/goodold-selfupdate

Start a new shell or reload your profile to get the correct paths:

    $ source .bash_profile
