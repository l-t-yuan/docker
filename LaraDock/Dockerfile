FROM daocloud.io/centos:6.7
# use aliyun yum mirrors
RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
RUN curl -o CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
RUN mv CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo
RUN yum update -y
# extend yum
RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
RUN rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm
#install php
RUN yum install -y \
php70w php70w-opcache \
php70w-fpm \
php70w-mysql \
php70w-mbstring \
php70w-mcrypt \
php70w-devel \
php70w-dom
# install wget , unzip , gcc
RUN yum install -y wget unzip gcc
# install php-redis
RUN wget -c https://github.com/phpredis/phpredis/archive/php7.zip
RUN unzip php7.zip
RUN cd phpredis-php7 && /usr/bin/phpize
RUN cd phpredis-php7 && ./configure --with-php-config=/usr/bin/php-config
RUN cd phpredis-php7 && make && make install
RUN echo '' >> /etc/php.ini
RUN echo 'extension="redis.so"' >> /etc/php.ini
RUN rm -rf phpredis-php7

RUN /etc/init.d/php-fpm restart
# remove wget , unzip , gcc
RUN yum remove -y wget unzip gcc
# install composer
ADD composer.phar composer.phar
RUN chmod a+x composer.phar
RUN mv composer.phar /usr/bin/composer
RUN /usr/bin/composer config -g repo.packagist composer https://packagist.phpcomposer.com
# install supervisor
RUN yum install -y supervisor
ADD supervisord.conf /etc/supervisord.conf
#install apache
RUN yum install -y httpd-devel
ADD httpd.conf /etc/httpd/conf.d/
# set Asia/Shanghai as localtime
RUN rm -rf /etc/localtime && cp -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
#install crontab
RUN yum install vixie-cron crontabs -y && yum clean all #last clean
ADD crontab /var/spool/cron/root
# start 80
EXPOSE 80

ADD init.sh /init.sh
RUN chmod 777 /init.sh
VOLUME /var/www/html

CMD ["/usr/bin/supervisord"]
