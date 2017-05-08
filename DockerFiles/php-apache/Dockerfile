FROM ubuntu:16.04

# install stuff
RUN apt-get update && apt-get install -y \
	apache2 libapache2-mod-php7.0 \
	php7.0-gd php7.0-json php7.0-mysql php7.0-curl php7.0-mbstring \
	php7.0-intl php7.0-mcrypt php-imagick php7.0-xml php7.0-zip

# Activation des mods
RUN a2enmod rewrite \
 && a2enmod headers \
 && a2enmod env \
 && a2enmod dir \
 && a2enmod mime

# Mise en place de l'environnement
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_CONFDIR /etc/apache2
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2

# create folders and set permissions
RUN \
	for dir in \
		"$APACHE_LOCK_DIR" \
		"$APACHE_RUN_DIR" \
		"$APACHE_LOG_DIR" \
		/var/www/html \
	; do \
		rm -rvf "$dir" \
		&& mkdir -p "$dir" \
		&& chown -R "$APACHE_RUN_USER:$APACHE_RUN_GROUP" "$dir"; \
	done

# Php
RUN { \
		echo '<FilesMatch \.php$>'; \
		echo '\tSetHandler application/x-httpd-php'; \
		echo '</FilesMatch>'; \
		echo; \
		echo 'DirectoryIndex disabled'; \
		echo 'DirectoryIndex index.php index.html'; \
		echo; \
		echo '<Directory /var/www/>'; \
		echo '\tOptions -Indexes'; \
		echo '\tAllowOverride All'; \
		echo '</Directory>'; \
	} | tee "$APACHE_CONFDIR/conf-available/php-apache.conf" \
	&& a2enconf php-apache

# logs should go to stdout / stderr
RUN ln -sf /dev/stdout "$APACHE_LOG_DIR/access.log" && \
    ln -sf /dev/stderr "$APACHE_LOG_DIR/error.log"

VOLUME [ "/var/www/html" ]
WORKDIR /var/www/html

EXPOSE 80
EXPOSE 443

ENTRYPOINT [ "/usr/sbin/apache2" ]
CMD [ "-D", "FOREGROUND" ]