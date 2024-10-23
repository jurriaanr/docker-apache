Based on https://hub.docker.com/_/httpd/
The "latest"-image is based on httpd:2.4-alpine

Personal apache setup using PHP-FPM, requires a PHP-FPM docker image: official ones from PHP or the custom build one in our other repository.
Note: If you don't need PHP (like only serving plain html) then we recommend using the vanilla HTTPD container or something lightweight like nginx/lighttpd.

# This image has the following modules enabled:
* mod_alias
* mod_rewrite
* mod_proxy_wstunnel
* mod_proxy_fcgi
* mod_proxy

# Additional configurations:
* mod_expires - with 1 month on image/css/javascript assets additional minetypes for svg and fonts
* mod_deflate - on text assets (html, css, js, svg, xml, plain) production settings on signatures

# Adding your own configurations:
Add your own configurations by adding entries to the web-container volumes under "/usr/local/apache2/conf/other/":
```
    web:
        volumes:
            - myconfiguration.conf:/usr/local/apache2/conf/other/myconfiguration.conf
```

Note that HTTPS is disabled so don't forget to enable that when you need it.

# Example docker-compose.yml:

```
services:
    web:
        image: jurriaanr/apache:2.4-fpm
        restart: always
        network_mode: "bridge"
        environment:
            - "VIRTUAL_HOST=my.domain.ext"
            - "APACHE_DOCUMENTROOT=/app/web"
        links:
            - php
        depends_on:
            - php
        volumes:
            - .:/app/:delegated
    php:
        image: jurriaanr/php:8.3-fpm
        restart: always
        network_mode: "bridge"
        volumes:
            - .:/app/:delegated
```

Switch PHP version by using another PHP image (exposed PHP-FPM on port 9000).

# Switch PHP container hostname or portnumber

You can use the PHP_FPM_HOSTNAME and PHP_FPM_PORT environment variables to change the hostname and
port Apache uses to connect to PHP-FPM:

```
version: '3'
services:
    web:
        ...SNIP
        environment:
            - "VIRTUAL_HOST=my.domain.ext"
            - "APACHE_DOCUMENTROOT=/app/web"
            - "PHP_FPM_HOSTNAME=my_php_container"
            - "PHP_FPM_PORT=9001"
```
