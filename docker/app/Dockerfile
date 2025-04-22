FROM ubuntu:24.04

LABEL maintainer="<group>"

ENV DEBIAN_FRONTEND=noninteractive \
    TZ=Asia/Brunei

# Install base dependencies and PHP 8.3 + Laravel-friendly extensions
RUN apt-get update && apt-get install -y \
    tzdata \
    curl \
    zip \
    unzip \
    git \
    supervisor \
    sqlite3 \
    nginx \
    php8.3 php8.3-fpm php8.3-cli \
    php8.3-mysql php8.3-sqlite3 \
    php8.3-mbstring php8.3-xml php8.3-bcmath php8.3-curl \
    php8.3-tokenizer php8.3-ctype php8.3-fileinfo php8.3-opcache \
    php8.3-readline \
    && ln -sf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo "$TZ" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata

# Set PHP 8.3 as default
RUN update-alternatives --set php /usr/bin/php8.3 \
    && update-alternatives --set phar /usr/bin/phar8.3 \
    && update-alternatives --set phar.phar /usr/bin/phar.phar8.3

# Install Composer from official image (secure + fast)
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Prepare directories
RUN mkdir -p /run/php \
    && echo "daemon off;" >> /etc/nginx/nginx.conf

# Log forwarding to Docker stdout/stderr
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

# Set working directory
WORKDIR /var/www/html

# Copy configuration files
COPY default /etc/nginx/sites-available/default
COPY php-fpm.conf /etc/php/8.3/fpm/php-fpm.conf
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY start-container /usr/bin/start-container

RUN chmod +x /usr/bin/start-container

# This is for documentation and clarity
EXPOSE 80

ENTRYPOINT ["start-container"]
