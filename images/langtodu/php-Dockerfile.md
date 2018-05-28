FROM php:7.2-fpm
#MAINTAINER goozp "gzp@goozp.com"
##  source: http://mp.weixin.qq.com/s/XVCw0OpEHSUwef4rcjnIXA

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 更新安装依赖包和PHP核心拓展
RUN apt-get update && apt-get install -y \
        git \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
        && docker-php-ext-install zip \
        && docker-php-ext-install pdo_mysql \
        && docker-php-ext-install opcache \
        && docker-php-ext-install mysqli \
        && rm -r /var/lib/apt/lists/*
# 将预先下载好的拓展包从宿主机拷贝进去
COPY ./pkg/redis.tgz /home/redis.tgz
COPY ./pkg/cphalcon.tar.gz /home/cphalcon.tar.gz
# 安装 PECL 拓展，这里我们安装的是Redis
RUN pecl install /home/redis.tgz && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini
# 安装第三方拓展，这里是 Phalcon 拓展
RUN cd /home \
    && tar -zxvf cphalcon.tar.gz \
    && mv cphalcon-* phalcon \
    && cd phalcon/build \
    && ./install \
    && echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/phalcon.ini
# 安装 Composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH
RUN rm -f /home/redis.tgz \
        rm -f /home/cphalcon.tar.gz 
WORKDIR /data
# Write Permission
RUN usermod -u 1000 www-data

## nginx配置
## server {
##     listen   80 default;
##     index index.html index.htm;
##     server_name localhost docker;
##     root /data/www;
##     index index.php index.html index.htm;
##     location / {
##         try_files $uri $uri/ /index.html;
##     }
##     location ~ \.php {
##         include fastcgi_params;
##         fastcgi_pass   php-fpm:9000;
##         fastcgi_index  index.php;
##         fastcgi_param  SCRIPT_FILENAME  /data/www/$fastcgi_script_name;
##     }
## }