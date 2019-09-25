# Documentation

## Netbox [Install](https://netbox.readthedocs.io/en/stable/installation/) on ubuntu

1. [PostgreSQL database](https://netbox.readthedocs.io/en/stable/installation/1-postgresql/)
    1. ubuntu install
    ```
    # apt-get update
    # apt-get install -y postgresql libpq-dev
    ```
    2. db creation
    ```
    # sudo -u postgres psql
    psql (9.4.5)
    Type "help" for help.

    postgres=# CREATE DATABASE netbox;
    CREATE DATABASE
    postgres=# CREATE USER netbox WITH PASSWORD 'J5brHrAXFLQSif0K';
    CREATE ROLE
    postgres=# GRANT ALL PRIVILEGES ON DATABASE netbox TO netbox;
    GRANT
    postgres=# \q
    ```
    3. verify db
    ```
    # psql -U netbox -W -h localhost netbox
    ```
    4. should get a 'netbox' prompt type \q to exit
2. [NetBox components](https://netbox.readthedocs.io/en/stable/installation/2-netbox/)
    1. Install componets
    ```
    # apt-get install -y python3 python3-pip python3-dev build-essential libxml2-dev libxslt1-dev libffi-dev graphviz libpq-dev libssl-dev redis-server zlib1g-dev
    ```
    2. clone release
    ```
    # mkdir -p /opt/netbox/ && cd /opt/netbox/
    # git clone -b master https://github.com/netbox-community/netbox.git .
    # chown -R netbox:netbox /opt/netbox/netbox/media/
    ```
    3. install python packages
    ```
    # pip3 install -r requirements.txt
    ```
    4. install NAPALM
    ```
    # pip3 install napalm
    ```
    5. edit configuration.py
    ```
    # cd netbox/netbox/
    # cp configuration.example.py configuration.py
    ```
    * ALLOWED_HOSTS
        ```
        ALLOWED_HOSTS = ['netbox.example.com', '192.0.2.123']
        ```
    * DATABASE
        ```
        DATABASE = {
            'NAME': 'netbox',               # Database name
            'USER': 'netbox',               # PostgreSQL username
            'PASSWORD': 'J5brHrAXFLQSif0K', # PostgreSQL password
            'HOST': 'localhost',            # Database server
            'PORT': '',                     # Database port (leave blank for default)
        }
        ```
    * REDIS
        ```
        REDIS = {
          'HOST': 'localhost',
          'PORT': 6379,
          'PASSWORD': '',
          'DATABASE': 0,
          'CACHE_DATABASE': 1,
          'DEFAULT_TIMEOUT': 300,
          'SSL': False,
        }
        ```
    * SECRET_KEY
    Generate a random secret key of at least 50 alphanumeric characters. This key must be unique to this installation and must not be shared outside the local system. You may use the script located at netbox/generate_secret_key.py to generate a suitable key.
    6. run database migrations
    ```
    # cd /opt/netbox/netbox/
    # python3 manage.py migrate
    Operations to perform:
      Apply all migrations: dcim, sessions, admin, ipam, utilities, auth, circuits, contenttypes, extras, secrets, users
    Running migrations:
      Rendering model states... DONE
      Applying contenttypes.0001_initial... OK
      Applying auth.0001_initial... OK
      Applying admin.0001_initial... OK
    ```
    7. create a super user
    ```
    # python3 manage.py createsuperuser
    Username: admin
    Email address: admin@example.com
    Password:
    Password (again):
    Superuser created successfully.
    ```
    8. collect static files
    ```
    # python3 manage.py collectstatic --no-input

    You have requested to collect static files at the destination
    location as specified in your settings:

        /opt/netbox/netbox/static

    This will overwrite existing files!
    Are you sure you want to do this?

    Type 'yes' to continue, or 'no' to cancel: yes
    ```
    9. load initial data
    ```
    # python3 manage.py loaddata initial_data
    Installed 43 object(s) from 4 fixture(s)
    ```
    10. TEST application
    ```
    # python3 manage.py runserver 0.0.0.0:8000 --insecure
    Performing system checks...

    System check identified no issues (0 silenced).
    November 28, 2018 - 09:33:45
    Django version 2.0.9, using settings 'netbox.settings'
    Starting development server at http://0.0.0.0:8000/
    Quit the server with CONTROL-C.
    ```
3. [HTTP dameon](https://netbox.readthedocs.io/en/stable/installation/3-http-daemon/)
    1. install nginx
    ```
    # apt-get install -y nginx
    ```
    2. config nginx
    ```
    sudo vi /etc/nginx/sites-available/netbox
    ```
    with the template
    ```
    server {
      listen 80;

      server_name netbox.example.com;

      client_max_body_size 25m;

      location /static/ {
          alias /opt/netbox/netbox/static/;
      }

      location / {
          proxy_pass http://127.0.0.1:8001;
          proxy_set_header X-Forwarded-Host $server_name;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-Proto $scheme;
          add_header P3P 'CP="ALL DSP COR PSAa PSDa OUR NOR ONL UNI COM NAV"';
      }
    }

    ```
    3. delete default and create symlink
    ```
    # cd /etc/nginx/sites-enabled/
    # rm default
    # ln -s /etc/nginx/sites-available/netbox
    ```
    4. start nginx
    ```
    # service nginx restart
    ```
    
4. [LDAP auth - optional]()


### Netbox Resources

- [netbox-community - github](https://github.com/netbox-community/netbox
- [netbox - readthedocs](https://netbox.readthedocs.io/en/stable/)
- [Python Swagger Client for Netbox - github](https://github.com/jeremyschulman/netbox-pyswagger)
- []()
- []()
- []()
