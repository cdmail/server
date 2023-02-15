# DeepMail Server

![](https://img.shields.io/badge/Python-3.10.9-blue)
![](https://img.shields.io/badge/Django-4.1.5-%2344B78B)
![](https://img.shields.io/badge/REST%20Framework-3.14.0-%23A30000)
![](https://img.shields.io/badge/Swagger-OpenAPI%202.0-%23aaaa00)
![](https://img.shields.io/badge/LICENSE-MIT-%2300557f)

The open source program of temporal space messaging system
with end-to-end encryption. <br/>


## Installation
We must install three (03) programs:
1. `Python3` runtime;
2. Python virtual environment `venv`;
3. Database manager `PostgreSQL`;
4. Getting of project repository.

### Install `python3`

```sh
sudo apt install python3 python3-pip
```

You have to make sure of the version of python that is installed.
The version of python used is `python 3.10.9`.

### Install venv
You can install a python virtualenv program in two different ways.

```sh
sudo apt install python3-venv
```

OR

```sh
sudo pip3 install virtualenv
```

### Install `PostgreSQL`

```sh
sudo apt install postgresql postgresql-contrib
```

For using a `spacial database`, we must to install the following extension:

```sh
# PostGIS is an extension of PostgreSQL
# that allows to process the spacial data like the Polygons,
# the Points, ...
sudo apt install postgis
```

### Getting of project repository
You can clone this repository everywhere you want in your machine,
with the following command lines:

```sh
git clone https://github.com/CodiTheck/dmail && cd dmail
```

In this cloned directory, you will see the following structure:

```
.
├── LICENSE
├── README.md
├── requirements.txt
└── server
    ├── core
    │   ├── asgi.py
    │   ├── __init__.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    └── manage.py

2 directories, 9 files
```

## Configuration
1. Setting virtual environment;
2. Creating and setting of PostgreSQL database;
3. Dependences installation.

### Setting virtual environment
1. In your project root, if you have not already done so,
run one of the following commands to create a virtual environment.

```sh
python3 -m venv env
```

OR

```sh
virtualenv env -p python3
```

2. Launch environment

```sh
source env/bin/activate
```

3. You must execute the following command to install the basic dependences:

```sh
pip install -r requirements.txt
```

### Creating and setting of PostgreSQL database
The following `SQL` command lines allow to create a `PostgreSQL`
database for your application:

```sh
# To connect to PostgreSQL with ROOT user:
sudo su - postgres

```

```sh
# To connect to default database (postgres)
psql

```

Given your database name is `dm_db` and the username is `dmail`.

```sql
CREATE DATABASE dm_db;
CREATE USER dmail WITH ENCRYPTED PASSWORD 'your-secret-password-here';
ALTER ROLE dmail SET client_encoding TO 'utf8';
ALTER ROLE dmail SET default_transaction_isolation TO 'read committed';
ALTER ROLE dmail SET timezone TO 'Europe/Paris';
GRANT ALL PRIVILEGES ON DATABASE dm_db TO dmail;

-- configuration for testing database for Django
ALTER USER dmail CREATEDB;
ALTER ROLE dmail SUPERUSER;

-- connect to brydb
\c cbrdb

-- You must transform to spatial database.
CREATE EXTENSION postgis;
```

Finally, disconnect from PostgreSQL by pressing `CTRL + D` twice.


#### .env config

1. You have to create a `.env` file in the root of the server
from the `server/.env_example`:

```sh
cp server/.env_example server/.env
```

2. Insert the following information into `.env` file:

| FIELDS   | VALUES                    |
| ------   | --------------------------|
| DB_NAME  | dm_db                     |
| USERNAME | dmail                     |
| PASSWORD | your-secret-password-here |
| HOST     | 127.0.0.1                 |
| PORT     | 5432                      |

Here are the contents of the file `.env`:

```
DB_NAME=dm_db
USERNAME=dmail
PASSWORD=your-secret-password-here
HOST=127.0.0.1
PORT=5432

```

> If port `5432` does not work, then try port `5433`.



## Launching the server
- Execute the following command lines to make migrations of models into database. 
It's assumed that you are currently in project directory root `dmail`.

```sh
mkdir server/static;\
./server/manage.py makemigrations;\
./server/manage.py migrate
```

You will get the following result, if all works succefully :

```
No changes detected
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  
  ...
  
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying sessions.0001_initial... OK

```

- Then, create a super user that will be used to connect to admin space.

```sh
./server/manage.py createsuperuser
```

- Finally to start server, you must execute the following command line:

```sh
./server/manage.py runserver
```

The result is :

```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
December 21, 2022 - 22:07:54
Django version 3.2.6, using settings 'core.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.

```

We cant go it at this local host link [](http://127.0.0.1:8000/) or [](http://localhost:8000). <br/>
You can change the IP address and the port of the server with the following command line:

```sh
# With this command, we cant make the server listens
# on the IP address of your local network on the port 8080.
./server/manage.py runserver 0.0.0.0:8080
```

You will see :

```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
December 21, 2022 - 22:08:41
Django version 3.2.6, using settings 'core.settings'
Starting development server at http://0.0.0.0:8080/
Quit the server with CONTROL-C.

```

All work with successfully ! <br/>
To access it in this cas, you must execute the following command line, in first:

```sh
# IF YOU ARE USING LINUX
# show your IP address of your machine, if it's connected
# to your local network for example.
ifconfig
```

> For the people using **Windows**, use `ipconfig` insted of the command line above.

We cant go it at this local host [link](http://yourip:8080/).

## Usage


### Hosting

```sh
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out path_to_certificate.crt -keyout path_to_private_key.key
```

```conf
server {
    root /home/mokira3d48/cobra/;
    listen              443 ssl;
    server_name         cobra.com;
    # server_name         ip_address;
    keepalive_timeout   70;

    ssl_certificate     /path_to_certificate.crt;
    ssl_certificate_key /path_to_private_key.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;
        
    location / {
        proxy_set_header Host $http_host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_redirect off;
        proxy_pass http://127.0.0.1:8000;
    }
        
}
```

<br/>


