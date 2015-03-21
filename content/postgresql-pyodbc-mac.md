Title: Connecting to PostgreSQL in Python with PyOdbc in Mac OS X Yosemite
Category: Tips
Date: 2015-03-21

I spent an embarassing amount of time on this, so I thought I would document it.

    brew install postgresql unixodbc psqlodbc

Start up postgres:

    postgres -D /usr/local/var/postgres

Check that you can connect to the database:

    psql

Inside postgres, create a user with a password. You probably need to give them
permissions and such.

    create user snake with password 'like-i-would-tell-you';

Create a file `~/.odbcinst.ini`. Add the following content:

    [ODBC Drivers]
    postodbc = Installed

    [postodbc]
    Description     = PostgreSQL ODBC driver
    Driver          = /usr/local/lib/psqlodbcw.so
    Setup           = /usr/local/lib/psqlodbcw.so
    Debug           = 0

Create a file `~/.odbc.ini`. Add the following content:

    [ODBC Data Sources]
    gopostal = postodbc

    [ODBC]
    Trace = 0

    [gopostal]
    Driver      = /usr/local/lib/psqlodbcw.so
    ServerName  = localhost
    Port        = 5432
    Database    = postgres
    Username    = snake
    Password    = like-i-would-tell-you


The command `isql` is included with unixodbc.  It is good for sanity checking
your configuration:

    isql -v gopostal

Once that works, you can install [PyOdbc](https://github.com/mkleehammer/pyodbc).
Don't use PyPi, instead download the latest version from the website.  You will
need to edit PyOdbc's `setup.py` to make things work.  Open up setup.py. There
is this nice big section where they add the iodbc library and supress the
warnings that iodbc is deprecated. (How nice.)  Change it to use odbc instead
of iodbc as follows:

    elif sys.platform == 'darwin':
        settings['libraries'].append('odbc')

Then, build and install PyOdbc:

    pip install .

Now you should be able to connect using pyodbc:

    python -c 'import pyodbc; pyodbc.connect(dsn="gopostal")'
