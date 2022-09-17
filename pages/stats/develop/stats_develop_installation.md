---
title: Installation
sidebar: stats_sidebar
toc: true
permalink: stats_develop_installation.html
folder: stats/develop
---

## Introduction

This document describes the steps to install this application in your local
development environment, so that you can run and debug it locally.  You will
also be configuring your ability to deploy the application into its runtime
environment.

As you decide on configuration values during this installation, you should
record them.  The installation documentation will identify `{PLACEHOLDER}`
values, which you will replace with the actual values that you have chosen.
A convenient way to keep track of these is to print out the
[Cheat Sheet](stats_develop_cheatsheet) page and fill it out as you go.

## Development Editor Installation

You will need to have access to an interactive development environment
(IDE) to edit and run this application.  Choose one that has support
for modern Javascript and Typescript applications.  Among tools known
to work well:
* [Microsoft Visual Studio Code](https://code.visualstudio.com)
* [IntelliJ Idea](https://www.jetbrains.com/idea)

## Database Environment Installation

The following steps will guide you through installing and configuring
Postgres on your development computer.

### Install Postgres and Utilities Locally

Download and install the latest release of
[PostgreSQL](https://www.postgresql.org/download/) for your platform,
and install it.  Installation notes:
* When choosing the components to be installed, omit the Stack Builder.
  Everything else is required.
* When you enter a password for the *database superuser*, record it as the
  `{PGPASSWORD}` value on the Cheat Sheet.
* Add the directory containing the Postgres command line tools to the
  **PATH** environment variable for your shell.  On a Mac OSX system,
  this will be in a directory named something like `/Library/PostgreSQL/13/bin`.
* You may need to restart your command line window after making this change.
* Verify successful installation by executing the following shell command:

```shell
psql --version
```

### Create a Postgres Database User

Select (and write down on the Cheat Sheet) values for the database
username and database password (`{DBUSERNAME}` and `{DBPASSWORD}`)
that the application will use to access the database.  Next, create
the database user with the following shell command:

```shell
createuser --pwprompt {DBUSERNAME}
```
You will be prompted to enter the `{DBPASSWORD}` value that you have
selected.  For security reasons, this will not be echoed to your screen,
so you will need to enter it twice to make sure there were no typos.

### Create Application Database (plus Shadow and Test Databases)

Next, select (and write down as the `{DBNAME}` value on the Cheat Sheet)
a name for the database that will be used by this application.  Normally,
you will name the database after the application itself, but you can choose
any name you like.  Create the database with a shell command like this:

```shell
createdb --owner={DBUSERNAME} {DBNAME}
```

To verify that everything is working so far, make sure you can successfully
access the new database with a shell command like this:

```shell
psql --username={DBUSERNAME} {DBNAME}
```

You will be challenged for the corresponding password (the `{DBPASSWORD}` value),
and then shown a prompt that includes the database name.  Enter **\q** and
press *Enter* to exit this application.

The database configuration tool we will be using later (graphile-migrate) also
requires a shadow database that it can use when tables are created.  Create it
like this:

```shell
createdb --owner={DBUSERNAME} {DBNAME}_shadow
```

You will also need a third database on which you will execute tests:

```shell
createdb --owner={DBUSERNAME} {DBNAME}_test
```

## Application Environment Installation

### Install Git Command Line Tools

*Git* is a source code control management application that records
changes (as you make them) to the source code.

Pick the most recent binary release for your platform at
[Git](https://git-scm.com/downloads) and install it.  You may need to
restart your command line window after installation to pick up the
`PATH` changes.  After successful installation, verify the required
tools are available as follows:

```shell
git --version
```

### Install Node.JS Environment

*Node.JS* is the runtime environment that supports execution of
Javascript and Typescript based applications outside a web browser.
The server portion of this system is such an application.

Pick the most recent Long Term Support (LTS) release for your platform at
[Node.JS](https://nodejs.org/en) and install it.  You will need to restart
your command line window after installation to pick up the `PATH` changes.
After successful installation, verify that the required tools are available
as follows:

```shell
node --version
npm --version
```

### Install Node Global Dependencies

Several Node-based technologies must be installed globally, because
they add executable commands we will need later.  Execute the following
commands from the command line.

The [graphile-migrate](https://github.com/graphile/migrate) is used to
manage changes to the database, remembering what changes have been done
previously on any particular database.  Consult the documentation there
for how it operates.

```shell
npm install -g graphile-migrate
graphile-migrate --version
graphile-migrate init
```

### Install Application Source and Executables

The source code for this application is stored in a repository on
[GitHub](https://github.com), a very popular location for both open
source and closed source development projects.  Each application
is stored in it's own location with its own URL.

Navigate to the directory within which you want to install the
application , and execute this command:

```shell
git clone https://github.com/cityteam/stats.git
```

This will download the relevant source code into a subdirectory
(named "stats" in this case).  Change your current directory to
the subdirectory for the next steps.

### Build Application Components

What we have downloaded is just the source code for the application.
Next, we will utilize the Node.JS development tools to build both
the server portion and the client portion, with the following commands:

```shell
npm install
npm run server:build
cd client
npm install
npm run build
cd ..
```

### Seed Database Table Structure

Next, we will execute "migration" utilities that will change our empty database
into one that contains the table structures required by this application.
In order to do this, we must first set up some environment variables.

In a command line window, issue the following commands (on a Windows platform,
use the "set" command instead of "export"), replacing the placeholders with
the values you previously recorded:

```shell
export DATABASE_URL="postgres://{DBUSERNAME}:{DBPASSWORD}@{DBHOST}:{DBPORT}/{DBNAME}"
export SHADOW_DATABASE_URL="postgres://{DBUSERNAME}:{DBPASSWORD}@{DBHOST}:{DBPORT}/{DBNAME}_shadow"
export ROOT_DATABASE_URL="postgres://{PGUSERNAME}:{PGPASSWORD}@{DBHOST}:{DBPORT}/postgres"
```

Now, execute the following command to perform the defined migrations (steps that
are executed in order to bring the database up to the latest state):

```shell
graphile-migrate migrate
```

You can verify that everything worked by using the Postgres *psql* tool:

```shell
$ psql --username={DBUSERNAME} {DBNAME}
{DBNAME}=# \dt
```

You can investigate the details of how any particular table is set up,
including indexes and foreign key relationships, by running the command

```sql
\d {tablename}
```

### Set Up Execution Configuration

Next, we will set up *environment variable* configurations that define
how the application will execute.  In a production installation,
only the *production* environment needs to be defined.  A developer,
on the other hand, will require all three.

**WARNING:  THESE FILES WILL CONTAIN SENSITIVE INFORMATION
LIKE PASSWORDS, AND SHOULD *NEVER* BE CHECKED IN TO GIT!
MAKE SURE THESE FILENAMES ARE LISTED IN THE *.gitignore*
FILE IN THE TOP LEVEL DIRECTORY OF THE APPLICATION!**

| NODE_ENV Value | Configuration Filename  | Description                                                                            |
|----------------|-------------------------|----------------------------------------------------------------------------------------|
| production     | .env.production         | Choices for production usage                                                           |
| development    | .env.development        | Convenient choices for development                                                     |
| test           | .env.test               | Configuration for running tests (use the test dataqbase!)                              |
| (none)         | .env                    | Should be a copy of the test configuration due to limitations in configuration support |

The variables that may be set in these environment configurations
will vary depending on the actual application, but the following
will be typical:

| Environment Variable | Description                                                                                                 |
|----------------------|-------------------------------------------------------------------------------------------------------------|
| ACCESS_LOG           | Destination for HTTP style access logs (filename, stdout, stderr, or none)                                  |
| CLIENT_LOG           | Destination for application logs from the client (filename, stdout, stderr, or none)                        |
| DATABASE_URL         | postgres://{DBUSERNAME}:{DBPASSWORD}@{DBHOST}:{DBPORT}/{DBNAME}                                             |
| HTTPS_CERT           | Pathname to the HTTPS certificate *fullchain.pem* file (if HTTPS supported)                                 |
| HTTPS_KEY            | Pathname to the HTTPS private key *privkey.pem* file (if HTTPS supported)                                   |
| OAUTH_ENABLED        | Are OAuth permissions enforced by the server? (true or false)                                               |
| PORT                 | Network port {APPPORT} on which the server should listen for non-HTTPS connections (not enabled if missing) |
| PORT_HTTPS           | Network port on which the server should listen for HTTPS connections (not enabled if missing)               |
| SERVER_LOG           | Destination for application logs from the server (filename, stdout, stderr, or none)                        |
| SUPERUSER_SCOPE      | OAuth scope that provides "superuser" access (typically "superuser")                                        |

If this is a development environment, in test mode point your DATABASE_URL
at the test database by appending "_test" after {DBNAME}.  Do this in both
*.env.test* and *.env*.

For log file settings, a filename will cause a file with that name to be
created in the *log* subdirectory, and the files will be rotated daily to
a filename that includes the date and time of the rotation.

As a developer, my personal preference is to set all three log configurations
to "stdout" in development and test modes, and setting OAUTH_ENABLED to false
in development mode (but true in test mode).  However, you can configure them
as you like based on your own preferences.

Setting up HTTPS support requires acquiring an HTTPS certificate, as
well as keeping it up to date when it expires.  Detailed instructions are
beyond the scope of this document, but [Let's Encrypt](https://letsencrypt.org)
is a good source of zero-cost certificates for developers.

### Seed A Superuser User

When the database was initially set up in the previous steps, all the
tables, including the table of valid users, are empty.  We need to add
a superuser user account, in order to set up things like other users, and
all other operations that are restricted to require superuser access.  We
will remedy that deficiency manually:

#### Start the server in development mode

In a command line window, execute the following command:

```shell
npm run start:dev
```

This will start the server using the *.env.development* configuration file's
values.  If you followed the recommended pattern, you would have included
*OAUTH_ENABLED=false* in these settings.  That is mission critical here,
because we cannot get permissions for the calling user to perform the next
task, when there are no valid users.

#### Use an HTTP tool to create the superuser user:

Using Postman (or, you can do this with command line tools like *curl* if you wish),
set up a POST transaction to *http://{APPHOST}:{APPPORT}/api/users* with
a body content type of *application/json* and the following contents:

```json
{
  "name": "Superuser User",
  "scope": "{SUPERUSER_SCOPE}",
  "username": "{SUUSERNAME}",
  "password": "{SUPASSWORD}"
}
```

Send this command to the server, and you should get a 201 CREATED response,
which will include an *id* assigned to the new user (probably 1), and **NO**
included password (for security reasons, the server will never send back
a password).

If you use *psql* to look inside the database, and do *select * from users;*,
you will see that the new user has been stored, but the password field has
been hashed to some unreadable value.  This will occur on any and all users
you add later, as well.  The hashing that took place works only in one direction
-- there is no way to translate back to the original password, so be sure you
remember the password that has been set!

### Verify Client Application Works

While the server is still running, open a browser tab and navigate to
*http://{APPHOST}:{APPPORT}* there.  You should see the application's
user interface, including a *Log In* button.  Click that button, and use
the superuser username and password that were just set up to log in.

If you run into problems, you can use *psql* to delete the database row
previously set up for the superuser user, and repeat the previous step
to create a new one.

When you are through, use Control+C in the server's command line window
to terminate it.


## (Optional) Set Up Run Shortcuts

If your development environment supports it, you will find it convenient
to set up shortcuts that will execute the various NPM scripts for either
the server or the client (these are defined in the *package.json* files).

For the server, I personally create shortcuts for each of the *develop:dev*,
*develop:prod*, *test*, and *test:coverage* scripts.  For the client,
I personally create shortcuts for the *start*, *build*, and *test* scripts.

