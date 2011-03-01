================================================================================
MarkUS development on Mac OS X - Snow Leopard
================================================================================

.. contents::

Setting up Ruby and Ruby on Rails
================================================================================

Run the following commands. You will be required to enter the Administrator
password.::

    #>sudo gem update --system
    #>sudo gem install rails -v 2.3.8
    #>sudo gem update rake
    #>sudo gem install mongrel ruby-debug fastercsv will_paginate factory_data_preloader faker
    #>sudo gem install machinist --source http://gemcutter.org

If you get a message saying "Missing these required gems", then it is likely
that some new gems have been integrated into Markus development and also need
to be installed using ``gem install`` as described above (or use ``rake
gems:install``;

Note: rake gems:install might not work correctly, because of a
rake chicken/egg problem. It seems rake requires the environment task, but
this doesn't work, because there are missing required gems. See ticket #635).
Edit this page to include them!

Setting up the Database
================================================================================

MarkUs works with either SQLite3, PostgreSQL, or MySQL databases.

Setting up SQLite3
--------------------------------------------------------------------------------

WARNING: Do not use SQLite3 for production environments.

To install SQLite3 run the following command::

    #>gem install sqlite3-ruby

Setting up PostgreSQL
--------------------------------------------------------------------------------

To install postgres 8.3, you can use the one-click installer on the following
site : [[Postgresql 8.3 One-Click Installer |
http://www.postgresql.org/download/macosx]]

You can also follow the instruction on this site : [[Postgresql 8.3 MacPorts
installation |
http://shifteleven.com/articles/2008/03/21/installing-postgresql-on-leopard-using-macports]]

After setting up the database, you need to create a user. Enter the following
commands::

    #>psql postgres postgres
    #>create user olm_db_admin with superuser password 'olm_db_admin';

The above command should output the following::

    CREATE ROLE

Finally, we need to change a line in the configuration file of the PostgreSQL
database. As root open "pg_hba.conf" (sometimes "pg_hdb.conf") in
``/etc/postgres/\<pg-version\>/main/`` and look for the following lines (the
first one is actually only a comment)::

    # "local" is for Unix domain socket connections only
    local   all         all                               ident sameuser

Now change the second line like so::

    local   all         all                               md5

To test if everything went fine we try to connect to the "postgres" database
using our newly created user::

    #> psql postgres olm_db_admin

You will be asked for a password, so type "olm_db_admin". After that you
should see the console of PostgreSQL.

Installing the PostgreSQL Ruby gem:
--------------------------------------------------------------------------------

Next, you'll need to install the PostgreSQL Ruby gem by entering the following
command::

    #>sudo gem install ruby-pg

If this command doesn't work, you can use this one.::

    #>sudo env ARCHFLAGS="-arch x86_64 gem install pg

Setting up MySQL
================================================================================

To install MySQL, you can use the installer from this site: [[MySQL Downloads |
(http://dev.mysql.com/downloads/mysql/5.1.html#macosx-dmg]] You can also
install MySQL using MacPorts instead by following the instructions on this
site: [[MySQL 5 MacPorts installation |
http://www.freerobby.com/2009/09/01/installing-mysql-via-macports-on-snow-leopard-for-ruby-development/]]

After the installation is complete, you'll need to update your
<code>PATH</code> environment variable. If you installed MySQL via the
installer, add <code>/usr/local/mysql/bin</code> to your <code>PATH</code>. If
you installed MySQL via MacPorts, add <code>/opt/local/lib/mysql5/bin</code>
to your <code>PATH</code>. 

Creating a database user
--------------------------------------------------------------------------------

To create a database user, enter the following commands: (In this example, the
user is named 'markus', his password is 'markus', and he will be given
superuser privileges. This user will be used for MarkUs later on.)::

    #>mysql --user=root mysql
    #>CREATE USER 'markus'@'localhost' IDENTIFIED BY 'markus';
    #>GRANT ALL PRIVILEGES ON *.* TO 'markus'@'localhost' WITH GRANT OPTION;

You can now try connecting to the server using the user you just created::

    #>mysql --user=markus --password=markus

You should see the MySQL console.

Installing the MySQL Ruby gem
--------------------------------------------------------------------------------

Next, you need to install the MySQL gem by entering the following command::

    #>sudo env ARCHFLAGS="-arch x86_64" gem install mysql -- --with-mysql-config=/usr/local/mysql/bin/mysql_config

Note: If you installed MySQL via MacPorts, the path to <code>mysql_config</code> should be: <code>/opt/local/lib/mysql5/bin/mysql_config</code>

Configuring the database connection settings
================================================================================

If you are using SQLite3, save the following text as config/database.yml (in
the MarkUs root directory)::

    # SQLite version 3.x
    #   gem install sqlite3-ruby (not necessary on OS X Leopard)
    development:
      adapter: sqlite3
      database: db/development.sqlite3
      pool: 5
      timeout: 5000

    # Warning: The database defined as "test" will be erased and
    # re-generated from your development database when you run "rake".
    # Do not set this db to the same as development or production.
    test:
      adapter: sqlite3
      database: db/test.sqlite3
      pool: 5
      timeout: 5000

    production:
      adapter: sqlite3
      database: db/production.sqlite3
      pool: 5
      timeout: 5000

If you are using PostgreSQL, enter the following command (from the MarkUs root
directory)::

    #>cp config/database.yml.postgresql config/database.yml

If you are using MySQL, enter the following command (from the MarkUs root
directory)::

    #>cp config/database.yml.mysql config/database.yml

You can then uncomment the "development" section of <code>config/database.yml</code>.

Subversion-rubybindings
================================================================================

Binary
--------------------------------------------------------------------------------

Install [[Subversion Bindings Binary |
http://www.open.collab.net/downloads/community/]].  Then add the following
lines to config.load_paths in config/environment.rb (from the MarkUs root
directory)::

    /opt/subversion/lib/svn-ruby
    /opt/subversion/lib/svn-ruby/universal-darwin/

DarwinPort
--------------------------------------------------------------------------------

Follow the instructions on [[DarwinPort Subversion-rubybindings |
http://subversion-rubybindings.darwinports.com/]].

Install the Radrails Plug-in for Eclipse
================================================================================

This tutorial assumes that you have a working installation of Eclipse IDE
(preferably Ganymede or later). After having a working Java installation this
step should be pretty easy (I usually install the provided Java packages of my
distribution). It is suggested to install Eclipse into one's home directory,
since Eclipse's built-in plug-in installation system works most seamlessly
that way. Downloading the Eclipse tar-ball (for Linux of course) and
extracting it in your home directory should suffice. You may want to add the
path where your eclipse executable resides to your PATH variable.

Install Aptana Radrails
--------------------------------------------------------------------------------

* Start Eclipse (as normal user, *not* root)
* Go to: “Help” - “Software Updates”
* Select “Available Software”
* Click on “Add Site”
* Enter Location: “http://update.aptana.com/update/studio/3.4/”
* Select (check) “Aptana Studio Installer for Eclipse 3.4” from "http://update.aptana.com/update/studio/3.4/"
* Click "Install..." and click the “Next >” button
* Read the License Agreement, accept the terms, and click the “Finish >” button.
* When it is recommended that Eclipse be restarted click “Yes”.
* After the restart, you will be asked to install something from Aptana Studio Site
* Select (check) from "Eclipse Integration" - "Aptana Web Development Tools" and click "Next >"
* Read the License Agreement, accept the terms, and click the “Next >” button.
* The downloads should be installed into the .eclipse folder in your home directory by default. If this is acceptable click the “Finish” button.
* Wait for the downloads to complete.
* Once the downloads are complete click the “Install All” button on the “Verification” screen.
* When it is recommended that Eclipse be restarted click “Yes”.
* Once Eclipse has been restarted a "My Aptana" screen will appear after switching to "Workspace".
* Click on "Plugins" and then on the "Get It" link of "Aptana RadRails"
* From "Site providing Aptana RadRails" - "Rails" select (check) "Aptana RadRails" and click "Next >"
* Read the License Agreement, accept the terms, and click the “Next >” button.
* Again, click "Next >"
* The downloads should be installed into the .eclipse folder in your home directory by default. If this is acceptable click the “Finish” button.
* Wait for the downloads to complete.
* Once the downloads are complete click the “Install All” button on the “Verification” screen.
* When it is recommended that Eclipse be restarted click “Yes”.

**Check Ruby and Rails Configuration**

If you are asked if you want to auto-install some gems it is up to you to install them or not (I did). 

* Go to "Window" - "Preferences"
* Select "Ruby" - "Installed Interpreters"
* The selected Ruby interpreter should be in /usr
* Now, go to "Rails"
* Rails should be auto-detected as well as Mongrel

Install Subclipse
--------------------------------------------------------------------------------

* Again go to: “Help” - “Software Updates”
* Select “Available Software”
* Click on “Add Site”
* Enter Location: “http://subclipse.tigris.org/update_1.2.x” (depending on which version you want to install; for me version 1.2.x worked best)
* Select (check) “Subclipse” from "http://subclipse.tigris.org/update_1.2.x"
* Click "Install..." and click the “Next >” button
* Read the License Agreement, accept the terms, and click the “Finish >” button.
* The downloads should be installed into the .eclipse folder in your home directory by default. If this is acceptable click the “Finish” button.
* Wait for the downloads to complete.
* Once the downloads are complete click the “Install” button on the “Verification” screen.
* When it is recommended that Eclipse be restarted click “Yes”.
* After installation go to "Window" - "Preferences" and select "Team" - "SVN" (there might be an Error message popping up, but you can ignore it)
* Now, in the "SVN interface" section select "SVNKit (Pure Java)" instead of "JavaHL (JNI)" and click "Apply"

Checkout out the MarkUs Source Code
--------------------------------------------------------------------------------

* Start Eclipse and switch to the RadRails perspective
* Go to "File" - "New" and select "Project..."
* At the "New Project" wizard select "SVN" - "Checkout Projects from SVN" and click on "Next >"
* Use "Create a new repository location" and click "Next >"
* Enter URL: "https://stanley.cdf.toronto.edu/svn/csc49x/olm_rails" and click "Next >"
* Accept the "invalid certificate warning" and select "trunk" - "checkmark" and click "Next >"
* Keep the default options and click "Finish"
* At the "New Project" wizard select "Rails" - "Rails Project" and click "Next >"
* Enter a project name of your choosing, deselect "Generate Rails application skeleton" and "Automatically start server after project is created"
* Click "Finish" and let Subclipse checkout the code from the repository

Install Xcode
================================================================================

You can also use Xcode for development. Xcode can be downloaded from this
site: [[Download Xcode | http://developer.apple.com/technology/xcode.html]]

Getting started with Xcode
--------------------------------------------------------------------------------

* Create a new Xcode project and then select "Window" -> "Organizer"
* Navigate to the directory containing the MarkUs source code in a Finder window
* Drag your MarkUs source code directory from the Finder window into the Organizer window
* That's it! 
* To run rake tasks, hold down the "Action" toolbar item and you'll be presented with a list of rake tasks that you can invoke

Some more information about getting started with Xcode can be found here:
[[Developing Rails Applications using Xcode |
(http://developer.apple.com/Tools/developonrailsleopard.html]]
You can also find step-by-step instructions for working with Xcode and
Subversion here: [[Using Subversion with Xcode 3 |
http://developer.apple.com/mac/articles/server/subversionwithxcode3.html]]
(The repository that contains the MarkUs source code can be found here: https://stanley.cdf.toronto.edu/svn/csc49x/olm_rails/trunk)

Getting Started with MarkUs Development
================================================================================

Create an environment variable called <code>RAILS_ENV</code> and set it to
<code>development</code>::

    #>export RAILS_ENV="development"

Start your newly installed RadRails and by using the "Ruby Explorer" navigate
to folder "config". Open the "database.yml" file and modify the "username:
..." and "password: ..." lines as follows::

    username: olm_db_admin
    password: olm_db_admin

(Make sure the username and password you specify match the username and
password you set when you created a user.)

Do that for "development", "test" and "production" and save your modified
"database.yml".

Next, we need to create a test, development, and production database. Run the
following commands::

    #>psql postgres postgres
    #>create database markus_test;
    #>create database markus_production;
    #>create database markus_development;

Alternatively, you can run one of the following commands which work for both
PostgreSQL and MySQL::

    #>rake db:create:all     # creates all the databases defined in config/database.yml
    #>rake db:create         # creates the database defined in config/database.yml for your current RAILS_ENV

Modifying config/environment.rb
================================================================================

The RAILS_GEM_VERSION is different in MAC OS X. Modify line 4 in
config/environnement.rb in MarkUs for::

    #>RAILS_GEM_VERSION = '2.3.3' unless defined? RAILS_GEM_VERSION

If your version differs (rails --version), use yours.

SVN repositories are used to contain files for every student groups. You need
to modify the REPOSITORY_STORAGE for a folder you created on your hard drive.

Rake tasks
================================================================================

Next, you can execute some rake tasks to test your MarkUs installation.
Sometimes, the "Rake Tasks" view doesn't work in RadRails but you can run the
commands from the Terminal.

Open the Terminal and <code>cd</code> to the MarkUs root directory. Then,
enter the following commands:

If you're using PostgreSQL::

    #>rake db:migrate

If you're using MySQL::

    #>rake db:schema:load

Next, you can load the initial database models for the current environment::

    #>rake db:populate

Next, you can create an instructor to log into MarkUs::

    #>rake markus:instructor user_name=Admin first_name=Admin last_name=Istrator

You can use this command to add students::

    #>rake markus:add_students

Now, start the server using::

    #>script/server

Another rake task that might be useful if you ever want to drop and recreate
the database from db/schema.rb::

    #>rake db:reset

You can learn more about other rake tasks by entering::

    #>rake -T


You should now be able to access MarkUs at http://localhost:3000 in your browser.

**Happy Coding!**
