# Configuring Basement

The directory `/Configuration/etc/` and its subdirectories contain example files for configuring Basement on a web server.

## Server Assumptions

The included configuration files assume that Basement is running on a Unix-like system, using for its web server [Lighttpd](https://lighttpd.net/), and for process control [supervisord](http://supervisord.org), against a [PostgreSQL](https://postgresql.org/) database.

## User Configuration

The Basement driver expects to run as a dedicated user, called `basement`. This user can have its own group, but it may be useful to have it be a member of the group `www-data` (or equivalent).

## Database Configuration

The Basement driver connects to a PostgreSQL database running on the local machine, and the database `basement` and the database user `basement` to be configured and use the `trust` connection authentication mode. The following is an example line from `pg_hba.conf`:

```
local   basement        basement                                trust
```

## Web Server Configuration

An example `lighttpd` configuration file is found in `etc/lighttpd/conf-enabled/99-basement.conf.example`. It assumes a webroot in `/var/basement/www` specific to Basement, and that processes like certificate renewal (e.g. using [Certbot](https://certbot.eff.org/) use a different system-wide web root, such as `/var/www`). If this is the case, simply copy the file, removing `.example` from the name, and run `lighttpd-enable-mod basement`.

## Process Control Configuration

An example `supervisord` configuration file is found in `etc/supervisor/conf.d/basement.conf.example`. It assumes that the Basement driver is installed to `/var/basement/bin/basement` and that it can log to `/var/log/basement`. If this is the case, simply copy the file, removing `.example` from the name, and run `service supervisor reload && supervisorctl start basement`.
