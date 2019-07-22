# uWSGI multiple python versions

After doing the below, I replaced uWSGI with this:
http://bradenmacdonald.com/blog/2015/uwsgi-emperor-multiple-python

# virtualenv

`pip` manages Python packages from the Python Package Index. Python's older package manager, `easy_install`, is installed when you install `distribute` or `setuptools`.

Install \$VirtualEnv \$dgdfg

    #sudo easy_install pip
    #sudo pip install virtualenv
    sudo apt-get install python3-pip
    sudo pip3 install virtualenv

Create new environment

    cd ~/PROJECT_NAME/
    virtualenv ENVIRONMENT_NAME

Use by doing either:

    ENVIRONMENT_NAME/bin/pip install requests

Or:

    source ENVIRONMENT_NAME/bin/activate
    pip install requests

`pip` has [requirements files](http://www.pip-installer.org/en/latest/cookbook.html#requirements-files) to allow automatic installing of dependencies by `env/bin/pip install -r requirements.txt`.

# Django

    virtualenv PROJECT_NAME
    cd PROJECT_NAME
    source bin/activate
    pip install Django
    django-admin.py startproject WEBSITE_NAME
    cd WEBSITE_NAME
    vim WEBSITE_NAME/settings.py
    # Add: STATIC_ROOT = os.path.join(BASE_DIR, "static/")
    python manage.py collectstatic

# uWSGI

    sudo apt-get install python2.7-dev
    pip install uwsgi

Test with a file `test.py`:

    uwsgi --http :8000 --wsgi-file test.py
    uwsgi --socket WEBSITE_NAME.sock --wsgi-file test.py --chmod-socket=666

Test a Django site first

    python manage.py runserver 0.0.0.0:8000

Run a Django site

    uwsgi --http :8000 --module WEBSITE_NAME.wsgi
    uwsgi --socket WEBSITE_NAME.sock --module WEBSITE_NAME.wsgi --chmod-socket=664

Set up config file: `vim WEBSITE_NAME_uwsgi.ini`

	# WEBSITE_NAME_uwsgi.ini file
	[uwsgi]
	
	# Django-related settings
	# the base directory (full path)
	chdir           = /home/vagrant/uwsgi-tutorial/WEBSITE_NAME
	# Django's wsgi file
	module          = WEBSITE_NAME.wsgi
	# the virtualenv (full path)
	home            = /home/vagrant/uwsgi-tutorial/
	
	# process-related settings
	# master
	master          = true
	# maximum number of worker processes
	processes       = 10
	# the socket (use the full path to be safe
	socket          = /tmp/WEBSITE_NAME.sock
	# ... with appropriate permissions - may be needed
	chmod-socket    = 666
	# clear environment on exit
	vacuum          = true
	daemonize = /home/vagrant/uwsgi-tutorial/WEBSITE_NAME/uwsgi.log
	#limit-as = 128 # when uncommented, this caused 502 error http://lists.unbit.it/pipermail/uwsgi/2013-July/006189.html
	max-requests = 5000
	#pidfile = /tmp/project-master.pid
	harakiri = 20 # respawn processes taking more than 20 seconds
	#env = DJANGO_SETTINGS_MODULE=WEBSITE_NAME.settings # set an environment variable

# nginx

Create config by `vim WEBSITE_NAME_nginx.conf`:

	 # WEBSITE_NAME_nginx.conf
	
	 # the upstream component nginx needs to connect to
	 upstream django {
	     server unix:///tmp/WEBSITE_NAME.sock; # for a file socket
	     #server 127.0.0.1:8001; # for a web port socket (we'll use this first)
	 }
	
	 # configuration of the server
	 server {
	     # the port your site will be served on
	     listen      8000;
	     # the domain name it will serve for
	     server_name .DOMAIN_NAME.TLD; # substitute your machine's IP address or FQDN
	     #server_name IP_ADDRESS;
	     charset     utf-8;
	
	     # max upload size
	     client_max_body_size 75M;   # adjust to taste
	
	     # Django media
	     location /media  {
	         alias /home/vagrant/uwsgi-tutorial/WEBSITE_NAME/media;  # your Django project's media files - amend as required
	     }
	
	     location /static {
	         alias /home/vagrant/uwsgi-tutorial/WEBSITE_NAME/static; # your Django project's static files - amend as required
	     }
	
	     # Finally, send all non-media requests to the Django server.
	     location / {
	         uwsgi_pass  django;
	         include     /home/vagrant/uwsgi-tutorial/WEBSITE_NAME/uwsgi_params; # the uwsgi_params file you installed
	     }
	 }

Do `vim uwsgi_params`:

	uwsgi_param  QUERY_STRING       $query_string;
	uwsgi_param  REQUEST_METHOD     $request_method;
	uwsgi_param  CONTENT_TYPE       $content_type;
	uwsgi_param  CONTENT_LENGTH     $content_length;
	
	uwsgi_param  REQUEST_URI        $request_uri;
	uwsgi_param  PATH_INFO          $document_uri;
	uwsgi_param  DOCUMENT_ROOT      $document_root;
	uwsgi_param  SERVER_PROTOCOL    $server_protocol;
	uwsgi_param  HTTPS              $https if_not_empty;
	
	uwsgi_param  REMOTE_ADDR        $remote_addr;
	uwsgi_param  REMOTE_PORT        $remote_port;
	uwsgi_param  SERVER_PORT        $server_port;
	uwsgi_param  SERVER_NAME        $server_name;

Then tell nginx about the config:

    sudo apt-get install nginx
    sudo /etc/init.d/nginx start
    sudo ln -s ~/path/to/your/WEBSITE_NAME/WEBSITE_NAME_nginx.conf /etc/nginx/sites-enabled/
    sudo /etc/init.d/nginx restart
    
# Switch uWSGI to systemwide

	deactivate # leave virtualenv
	sudo pip install uwsgi
	# create a directory for the vassals
	sudo mkdir -p /etc/uwsgi/vassals
	# symlink from the default config directory to your config file
	sudo ln -s /home/vagrant/uwsgi-tutorial/WEBSITE_NAME/WEBSITE_NAME_uwsgi.ini /etc/uwsgi/vassals/
	# run the emperor
	# uwsgi --emperor /etc/uwsgi/vassals --uid www-data --gid www-data
	# sudo uwsgi --emperor /etc/uwsgi/vassals --uid www-data --gid www-data

Create `/etc/init/uwsgi.conf`:

    # Emperor uWSGI script

    description "uWSGI Emperor"
    start on runlevel [2345]
    stop on runlevel [06]

    respawn

    exec uwsgi --emperor /etc/uwsgi/vassals --uid www-data --gid www-data

Not sure if we need the following 

    sudo initctl reload-configuration

# Entire process together

    sudo apt-get install python3-pip vim nginx libmysqlclient-dev
    sudo pip3 install virtualenv uwsgi
    cd ~
    virtualenv ENVIRONMENT_NAME
    cd ENVIRONMENT_NAME
    source bin/activate
    pip install Django uwsgi mysqlclient
    django-admin.py startproject PROJECT_NAME
    cd PROJECT_NAME
    echo "STATIC_ROOT = os.path.join(BASE_DIR, "static/")" >> PROJECT_NAME/settings.py
    python manage.py collectstatic
    vim PROJECT_NAME_uwsgi.ini
    # copy contents from directions
    vim PROJECT_NAME_nginx.conf
    # copy contents from directions
    vim uwsgi_params
    # copy contents from directions
    sudo ln -s ~/ENVIRONMENT_NAME/PROJECT_NAME/PROJECT_NAME_nginx.conf /etc/nginx/sites-enabled/
    sudo /etc/init.d/nginx restart
    sudo mkdir -p /etc/uwsgi/vassals
    sudo ln -s /home/vagrant/ENVIRONMENT_NAME/PROJECT_NAME/PROJECT_NAME_uwsgi.ini /etc/uwsgi/vassals/
    # uwsgi --emperor /etc/uwsgi/vassals --uid www-data --gid www-data
    sudo vim /etc/init/uwsgi.conf
    # copy contents from directions
    # not sure if we need the following 
    sudo initctl reload-configuration

## nginx.conf

    # PROJECT_NAME_nginx.conf
    upstream django {
      server unix:///tmp/PROJECT_NAME.sock; # for a file socket
    }
    # configuration of the server
    server {
      listen      8000;
      server_name .DOMAIN_NAME.TLD;
      charset     utf-8;
      client_max_body_size 75M;
      location /media  {
        alias /home/vagrant/ENVIRONMENT_NAME/PROJECT_NAME/media;
      }
      location /static {
        alias /home/vagrant/ENVIRONMENT_NAME/PROJECT_NAME/static;
      }
      location / {
        uwsgi_pass  django;
        include     /home/vagrant/ENVIRONMENT_NAME/PROJECT_NAME/uwsgi_params;
      }
    }

## uwsgi.ini

    # PROJECT_NAME_uwsgi.ini file
    [uwsgi]

    # Django-related settings
    # the base directory (full path)
    chdir           = /home/vagrant/ENVIRONMENT_NAME/PROJECT_NAME
    # Django's wsgi file
    module          = PROJECT_NAME.wsgi
    # the virtualenv (full path)
    home            = /home/vagrant/ENVIRONMENT_NAME/

    # process-related settings
    # master
    master          = true
    # maximum number of worker processes
    processes       = 10
    # the socket (use the full path to be safe
    socket          = /tmp/PROJECT_NAME.sock
    # ... with appropriate permissions - may be needed
    chmod-socket    = 666
    # clear environment on exit
    vacuum          = true
    daemonize = /home/vagrant/ENVIRONMENT_NAME/PROJECT_NAME/uwsgi.log
    limit-as = 128
    max-requests = 5000
    #pidfile = /tmp/PROJECT_NAME-master.pid
    harakiri = 20 # respawn processes taking more than 20 seconds
    #env = DJANGO_SETTINGS_MODULE=PROJECT_NAME.settings # set an environment variable

# Main source

[Setting up Django and your web server with uWSGI and nginx](https://uwsgi.readthedocs.org/en/latest/tutorials/Django_and_nginx.html)

# restarting

    cd ~/PROJECT_NAME/vm2
    vagrant up
    vagrant ssh
    
    cd ~/ENVIRONMENT_NAME/PROJECT_NAME
    source ../bin/activate
    
    sudo restart uwsgi
    
# Django notes

three-step guide to making model changes:

* Change your models (in `models.py`).
* Run `python manage.py makemigrations` to create migrations for those changes
* Run `python manage.py migrate` to apply those changes to the database.

# UWSGI and Apache

	apt-get install libapache2-mod-uwsgi

# Monitor app

	apt-get install python2.7-dev
	cd ~/www/projects
	virtualenv -p /usr/bin/python monitor
	cd monitor
	source bin/activate
	pip install --upgrade pip
	pip install utils db web.py

## install MongoDB (https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/)

	sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
	sudo apt-get update
	sudo apt-get install -y mongodb-org
	echo "mongodb-org hold" | sudo dpkg --set-selections
	echo "mongodb-org-server hold" | sudo dpkg --set-selections
	echo "mongodb-org-shell hold" | sudo dpkg --set-selections
	echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
	echo "mongodb-org-tools hold" | sudo dpkg --set-selections

## resolve SNI warning (https://urllib3.readthedocs.org/en/latest/security.html#snimissingwarning)

	sudo apt-get install build-essential python-dev libffi-dev libssl-dev
	pip install 'urllib3[secure]'
	
	pip install git+https://github.com/whilefalse/webpy-mongodb-sessions.git

## setup new project example

	$ cd /var/www/votecharlie.com/www/projects/
	$ projects  virtualenv -p /usr/bin/python2.7 foo
	Running virtualenv with interpreter /usr/bin/python2.7
	New python executable in foo/bin/python2.7
	Also creating executable in foo/bin/python
	Installing setuptools, pip, wheel...done.
	$ cp monitor/requirements.txt foo
	$ cd foo
	$ source bin/activate
	$ pip install -r requirements.txt

	cd <localdir>
	git init
	git add .
	git commit -m 'message'
	git remote add origin <url>
	git push -u origin master
