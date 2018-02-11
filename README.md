# Alpine LAP Server with Extensions

Provides a basic LAP stack using Alpine, Apache2 and PHP7, loading in the various extensions along the way (see Dockerfile for full list).

Should allow you to get going with a full LAP stack and support for DB via linked container (such as mysql) with ease, allowing you to fine tune various aspects of the server and php via environment variables.


## Included in this image

bash, apache2, php7, php7-apache2, curl, ca-certificates, git

php7-phar, php7-mcrypt, php7-soap, php7-openssl, php7-gmp, php7-pdo_odbc, php7-json, php7-dom, php7-pdo, php7-zip, php7-mysqli, php7-sqlite3, php7-pdo_pgsql, php7-bcmath, php7-gd, php7-odbc, php7-pdo_mysql, php7-pdo_sqlite, php7-gettext, php7-xmlreader, php7-xmlrpc, php7-bz2, php7-iconv, php7-pdo_dblib, php7-curl, php7-ctype, php7-session, php7-redis.


## Environment Variables

Various env vars can be set at runtime via your docker command or docker-compose environment section.

__APACHE_SERVER_NAME:__ Change server name to match your domain name in httpd.conf

__PHP_SHORT_OPEN_TAG:__ Maps to php.ini 'short_open_tag'

__PHP_OUTPUT_BUFFERING:__ Maps to php.ini 'output_buffering'

__PHP_OPEN_BASEDIR:__ Maps to php.ini 'open_basedir'

__PHP_MAX_EXECUTION_TIME:__ Maps to php.ini 'max_execution_time'

__PHP_MAX_INPUT_TIME:__ Maps to php.ini 'max_input_time'

__PHP_MAX_INPUT_VARS:__ Maps to php.ini 'max_input_vars'

__PHP_MEMORY_LIMIT:__ Maps to php.ini 'memory_limit'

__PHP_ERROR_REPORTING:__ Maps to php.ini 'error_reporting'

__PHP_DISPLAY_ERRORS:__ Maps to php.ini 'display_errors'

__PHP_DISPLAY_STARTUP_ERRORS:__ Maps to php.ini 'display_startup_errors'

__PHP_LOG_ERRORS:__ Maps to php.ini 'log_errors'

__PHP_LOG_ERRORS_MAX_LEN:__ Maps to php.ini 'log_errors_max_len'

__PHP_IGNORE_REPEATED_ERRORS:__ Maps to php.ini 'ignore_repeated_errors'

__PHP_REPORT_MEMLEAKS:__ Maps to php.ini 'report_memleaks'

__PHP_HTML_ERRORS:__ Maps to php.ini 'html_errors'

__PHP_ERROR_LOG:__ Maps to php.ini 'error_log'

__PHP_POST_MAX_SIZE:__ Maps to php.ini 'post_max_size'

__PHP_DEFAULT_MIMETYPE:__ Maps to php.ini 'default_mimetype'

__PHP_DEFAULT_CHARSET:__ Maps to php.ini 'default_charset'

__PHP_FILE_UPLOADS:__ Maps to php.ini 'file_uploads'

__PHP_UPLOAD_TMP_DIR:__ Maps to php.ini 'upload_tmp_dir'

__PHP_UPLOAD_MAX_FILESIZE:__ Maps to php.ini 'upload_max_filesize'

__PHP_MAX_FILE_UPLOADS:__ Maps to php.ini 'max_file_uploads'

__PHP_ALLOW_URL_FOPEN:__ Maps to php.ini 'allow_url_fopen'

__PHP_ALLOW_URL_INCLUDE:__ Maps to php.ini 'allow_url_include'

__PHP_DEFAULT_SOCKET_TIMEOUT:__ Maps to php.ini 'default_socket_timeout'

__PHP_DATE_TIMEZONE:__ Maps to php.ini 'date.timezone'

__PHP_PDO_MYSQL_CACHE_SIZE:__ Maps to php.ini 'pdo_mysql.cache_size'

__PHP_PDO_MYSQL_DEFAULT_SOCKET:__ Maps to php.ini 'pdo_mysql.default_socket'

__PHP_SESSION_SAVE_HANDLER:__ Maps to php.ini 'session.save_handler'

__PHP_SESSION_SAVE_PATH:__ Maps to php.ini 'session.save_path'

__PHP_SESSION_USE_STRICT_MODE:__ Maps to php.ini 'session.use_strict_mode'

__PHP_SESSION_USE_COOKIES:__ Maps to php.ini 'session.use_cookies'

__PHP_SESSION_COOKIE_SECURE:__ Maps to php.ini 'session.cookie_secure'

__PHP_SESSION_NAME:__ Maps to php.ini 'session.name'

__PHP_SESSION_COOKIE_LIFETIME:__ Maps to php.ini 'session.cookie_lifetime'

__PHP_SESSION_COOKIE_PATH:__ Maps to php.ini 'session.cookie_path'

__PHP_SESSION_COOKIE_DOMAIN:__ Maps to php.ini 'session.cookie_domain'

__PHP_SESSION_COOKIE_HTTPONLY:__ Maps to php.ini 'session.cookie_httponly'

__PHP_XDEBUG_ENABLED:__ Add this env and give it a value to turn it on, such as true, or On or Awesome, or beer, or socks... Turns on xdebug (which is not for production really)


## Usage

To use this image directly, you can use a docker-compose file to keep things nice and simple... if you have a load balancer like traefik and mysql containers running on another docker network, you may have something like this...


```yml
version: "2"
services:
  myservice:
    build: ./
    labels:
      - "traefik.backend=myservice"
      - "traefik.frontend.rule=Host:myservice.docker.localhost"
    environment:
      - MYSQL_HOST=mysql
      - APACHE_SERVER_NAME=myservice.docker.localhost
      - PHP_SHORT_OPEN_TAG=On
      - PHP_ERROR_REPORTING=E_ALL
      - PHP_DISPLAY_ERRORS=On
      - PHP_HTML_ERRORS=On
      - PHP_XDEBUG_ENABLED=true
    networks:
      - default
    volumes:
      - ./:/app
	# ADD in permission for setting system time to host system time
    cap_add:
      - SYS_TIME
      - SYS_NICE
networks:
  default:
    external:
      name: docker_docker-localhost
```

Then run...

```bash
docker-compose up -d
```

This will patch the container through to traefik load balancer running from another dc file.

If you would like to add to this, expand on this, maybe you don't want to map your volume and want to copy files for a production system. You can create your own Dockerfile based on this image...

```
FROM ulsmith/alpine-apache-php7
MAINTAINER You <you@youremail.com>

ADD /public /app/public
RUN chown -R apache:apache /app
```

## Where Do I Put My Files

Hmmm... you can place them in the /app folder, your application should be placed in the /app folder with public access being pushed through to /app/public. This alloows you to have your src files and other outside the public directory.
